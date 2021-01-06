spring-cache 自定义缓存过期时间

一、码前必备

1. spring cache简介
   
   缓存是实际工作中非经常常使用的一种提高性能的方法, 我们会在很多场景下来使用缓存。
   
   spring cache 非常强大，使用起来简单，spring cache 抽象了缓存实现，用户可以自由选择缓存实现。可能是为了保持接口的统一性未提供缓存过期时间设置，本文将使用redis实现一个简单的key级别的自定义过期时间案例。
   
   spring cache [官方文档地址](https://spring.io/guides/gs/caching/)，阅读本文前建议先了解spring cache的使用
   
2. 注意事项

   * 被缓存的对象必需有无参构造方法

3. 代码仓库

   spring boot starter：https://github.com/1024potato/study/tree/master/expiration-spring-cache-starter

   演示代码仓库：https://github.com/1024potato/study/tree/master/spring-cache-expiration-demo

二、 redis 实现

1. 实现原理
   * 重写缓存管理 org.springframework.cache.CacheManager
   * 注解@Cacheable  缓存key带上过期时间

2. 代码

   ````java
   package cn.kj120.study.expiration.spring.cache.starter;
   
   
   import com.fasterxml.jackson.annotation.JsonTypeInfo;
   import com.fasterxml.jackson.databind.ObjectMapper;
   import org.slf4j.Logger;
   import org.slf4j.LoggerFactory;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.boot.context.properties.EnableConfigurationProperties;
   import org.springframework.cache.CacheManager;
   import org.springframework.cache.annotation.EnableCaching;
   import org.springframework.context.annotation.Bean;
   import org.springframework.context.annotation.Configuration;
   import org.springframework.data.redis.cache.RedisCache;
   import org.springframework.data.redis.cache.RedisCacheConfiguration;
   import org.springframework.data.redis.cache.RedisCacheManager;
   import org.springframework.data.redis.cache.RedisCacheWriter;
   import org.springframework.data.redis.connection.RedisConnectionFactory;
   import org.springframework.data.redis.serializer.Jackson2JsonRedisSerializer;
   import org.springframework.data.redis.serializer.RedisSerializationContext;
   import org.springframework.util.StringUtils;
   
   import javax.annotation.PostConstruct;
   import java.time.Duration;
   import java.util.HashMap;
   import java.util.Map;
   
   /**
    * 缓存配置
    */
   @Configuration
   @EnableCaching
   @EnableConfigurationProperties(Cache.class)
   public class CacheConfig {
   
       private Logger log = LoggerFactory.getLogger(getClass());
   
       @Autowired
       private Cache cache;
   
       /**
        * 过期时间单位对应秒转换倍数 m -> 60  h-> 3600  d -> 86400
        */
       private Map<String, Long> ttlUnit = new HashMap<>(4);
   
       @PostConstruct
       public void init() {
           ttlUnit.put("s", 1L);
           ttlUnit.put("m", 60L);
           ttlUnit.put("h", 3600L);
           ttlUnit.put("d", 86400L);
       }
   
       @Bean
       public CacheManager cacheManager(RedisConnectionFactory redisConnectionFactory, Jackson2JsonRedisSerializer redisCacheSerializer){
           RedisCacheConfiguration redisCacheConfiguration = RedisCacheConfiguration.defaultCacheConfig()
                   // 设置默认有效期
                   .entryTtl(Duration.ofSeconds(ttlSecond(cache.getDefaultTtl())))
                   // 设置缓存key前缀
                   .computePrefixWith(cacheName -> cache.getPrefix() + cacheName)
                   // 设置存储格式
                   .serializeValuesWith(RedisSerializationContext.SerializationPair.fromSerializer(redisCacheSerializer));
   
           MyRedisCacheManager myRedisCacheManager = new MyRedisCacheManager(RedisCacheWriter.nonLockingRedisCacheWriter(redisConnectionFactory), redisCacheConfiguration);
           return myRedisCacheManager;
       }
   
       @Bean
       public Jackson2JsonRedisSerializer redisCacheSerializer() {
           Jackson2JsonRedisSerializer redisSerializer = new Jackson2JsonRedisSerializer<>(Object.class);
           ObjectMapper objectMapper = new ObjectMapper();
           // 序列化的json字符串里面包含包信息
           objectMapper.activateDefaultTyping(objectMapper.getPolymorphicTypeValidator(), ObjectMapper.DefaultTyping.NON_FINAL, JsonTypeInfo.As.WRAPPER_ARRAY);
   
           redisSerializer.setObjectMapper(objectMapper);
           return redisSerializer;
       }
   
   
       private class MyRedisCacheManager extends RedisCacheManager {
   
           public MyRedisCacheManager(RedisCacheWriter redisCacheWriter, RedisCacheConfiguration redisCacheConfiguration){
               super(redisCacheWriter, redisCacheConfiguration);
           }
   
           @Override
           protected RedisCache createRedisCache(String name, RedisCacheConfiguration cacheConfig) {
               log.info("缓存的key: {} ", name);
               String[] redisKeyArray = StringUtils.delimitedListToStringArray(name, cache.getDelimiter()          );
               name = redisKeyArray[0] + ":";
               // 设置过期时间单位秒
               if (redisKeyArray.length > 1){
                   long ttl = ttlSecond(redisKeyArray[1]);
                   cacheConfig = cacheConfig.entryTtl(Duration.ofSeconds(ttl));
               }
               return super.createRedisCache(name, cacheConfig);
           }
       }
   
       /**
        * 单位转换成秒数
        * @param ttlString
        * @return
        */
       private Long ttlSecond(String ttlString) {
           // 获取单位
           String unit = ttlString.substring(ttlString.length() - 1);
           // 获取单位对应的秒倍数
           Long multiple = ttlUnit.get(unit);
           if (multiple == null) {
               multiple = 1L;
           } else {
               ttlString = ttlString.substring(0, ttlString.length() - 1);
           }
           return Long.parseLong(ttlString) * multiple;
       }
   
   }
   
   ````

   

三、使用演示

````java
// 设置5分钟缓存
@Cacheable(value = "user#5m", key = "#id")
public User find(Integer id) {
    User user = userMap.get(id);
    log.info("查询到用户 {}", user);
    return user;
}
````

