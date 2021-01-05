# 初识 Spring boot starter

## 什么是 Starter

​	Starter可以理解为一个可拔插式的插件，提供一系列便利的依赖描述符，您可以获得所需的所有Spring和相关技术的一站式服务。应用程序只需要在maven中引入starter依赖，SpringBoot就能自动扫描到要加载的信息并启动相应的默认配置。用一句话描述，就是springboot的场景启动器

## Starter 优点

## 自定义  Starter

1. 新建一个空的maven项目

2. 引入自动配置依赖包

   ```java
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-autoconfigure</artifactId>
       <version>2.3.0.RELEASE</version>
   </dependency>
   ```

3.	编写需要注入服务

    ```java
	package cn.kj120.study;

    public class DemoService {

        public String hello(String name) {
            return "hello " + name;
        }
    }

    ```
    
4.	配置文件
	```java
	package cn.kj120.study;
    import 	org.springframework.boot.autoconfigure.condition.ConditionalOnProperty;
    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;

    @Configuration
    // application.properties 配置文件中存在 demoStarter 配置才自动注入
    @ConditionalOnProperty(value = "demoStarter")
    public class StarterConfig {

        @Bean
        public DemoService demoService() {
            return new DemoService();
        }
    }
	```
	
5.	配置自动注入  
	
	在resources目录下，新建 META_INF/spring.factories 文件，填入需要自动注入类的全路径 cn.kj120.study.StarterConfig
	
	
	
	![image-20200717180952078](http://qiniu.kj120.cn/image-20200717180952078.png)

## Starter 规范

来自 Spring 官方的 starter 都是 以 `spring-boot-starter` 开头，比如:

- spring-boot-starter-web
- spring-boot-starter-aop

如果我们自定义 starter 功能名称叫 demo 命名是这样的:

- demo-spring-boot-starter

- demo-spring-boot-autoconfigure

## 配置ide自动提示

![image-20210105200431099](http://qiniu.kj120.cn/image-20210105200431099.png)

IDE是通过读取配置信息的元数据而实现自动提示的，而元数据在目录`META-INF`中的`spring-configuration-metadata.json` 或者 `additional-spring-configuration-metadata.json`

引入包

````xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
    <optional>true</optional>
</dependency>
````




## [官方Starter仓库地址](https://github.com/spring-projects/spring-boot/tree/v2.1.0.RELEASE/spring-boot-project/spring-boot-starters)	

## 第三方 Starter

| name                                     | description                                                  |
| ---------------------------------------- | ------------------------------------------------------------ |
| [MyBatis-Plus](https://mp.baomidou.com/) | [MyBatis-Plus](https://github.com/baomidou/mybatis-plus)（简称 MP）是一个 [MyBatis](http://www.mybatis.org/mybatis-3/) 的增强工具，在 MyBatis 的基础上只做增强不做改变，为简化开发、提高效率而生 |
| [Knife4j](https://doc.xiaominfo.com/)    | knife4j是为Java MVC框架集成Swagger生成Api文档的增强解决方案  |

