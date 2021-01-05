# gitbook 安装

1. 安装nodejs

   

   node下载地址: https://nodejs.org/zh-cn/download/

   

2. 快速开始

   * 全局 npm 安装 gitbook

   ````
   npm i -g gitbook-cli
   ````

   * 初始化gitbook

   ````npm
   gitbook init 书籍路径地址
   ````

   ​     初始化后生成以下文件

   ````
   README.md  书籍介绍
   SUMMARY.md  书籍目录结构
   ````

   * 修改目录，重新执行初始化书籍命令  gitbook init 书籍路径地址

     SUMMARY.md  示例

   ```
   # 目录
   
   * [前言](README.md)
   * [第一章 寻找伙伴](Chapter1/README.md)
     * [第1节：娜美](Chapter1/namei.md)
     * [第2节：索隆](Chapter1/suolong.md)
     * [第3节：乌索普](Chapter1/wusuopu.md)
     * [第4节：乔巴酱](Chapter1/qiaobajiang.md)
     * [第x节：](Chapter1/x.md)
   * [第x章 新世界](Chapter2/README.md)
     * [第x节：终章！雷鸣八卦](Chapter2/leimingbagua.md)		
   ```

   * 启动server    

     启动命令：gitbook serve 

     ````
     $ gitbook serve
     Live reload server started on port: 35729
     Press CTRL+C to quit ...
     
     info: 7 plugins are installed
     info: loading plugin "livereload"... OK
     info: loading plugin "highlight"... OK
     info: loading plugin "search"... OK
     info: loading plugin "lunr"... OK
     info: loading plugin "sharing"... OK
     info: loading plugin "fontsettings"... OK
     info: loading plugin "theme-default"... OK
     info: found 9 pages
     info: found 9 asset files
     info: >> generation finished with success in 0.8s !
     
     Starting server ...
     Serving book on http://localhost:4000
     ````

   * 浏览器访问地址 http://localhost:4000

     ![image-20210105165544867](http://qiniu.kj120.cn/image-20210105165544867.png)

   * 构建书籍

     ````
     语法: gitbook 选项 [书籍路径] [输出路径]
     html格式 -> gitbook build
     pdf格式 ->  gitbook pdf . ./book.pdf
     pdf格式 ->  gitbook epub . ./book.epub
     pdf格式 ->  gitbook mobi . ./book.mobi
     ````

     

3. 异常问题

* TypeError: cb.apply is not a function

````
$ gitbook serve
Live reload server started on port: 35729
Press CTRL+C to quit ...

D:\node-v12.20.1-win-x64\node_modules\gitbook-cli\node_modules\npm\node_modules\graceful-fs\polyfills.js:287
      if (cb) cb.apply(this, arguments)
                 ^

TypeError: cb.apply is not a function
    at D:\node-v12.20.1-win-x64\node_modules\gitbook-cli\node_modules\npm\node_modules\graceful-fs\polyfills.js:287:18
    at FSReqCallback.oncomplete (fs.js:169:5)

````

* windows 系统 GitBook生成PDF、epub报错Error during ebook generation: 'ebook-convert' 乱码

````
$ gitbook pdf . ./book.pdf
info: 7 plugins are installed
info: 6 explicitly listed
info: loading plugin "highlight"... OK
info: loading plugin "search"... OK
info: loading plugin "lunr"... OK
info: loading plugin "sharing"... OK
info: loading plugin "fontsettings"... OK
info: loading plugin "theme-default"... OK
info: found 9 pages
info: found 9 asset files

EbookError: Error during ebook generation: 'ebook-convert' �����ڲ����ⲿ���Ҳ���ǿ����еĳ���
���������ļ���
````

解决办法: 安装 Calibre 

下载地址：https://pc.qq.com/detail/16/detail_1916.html

