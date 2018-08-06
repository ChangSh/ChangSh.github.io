---
title: Nginx安装部署及映射本地资源目录

### Linux 部署nginx

***环境： 百度云实例  CentOS / 7.5 x86_64 (64bit)***

* **安装依赖**
    * yum install gcc
    * yum install pcre-devel
    * yum install zlib zlib-devel
    * yum install openssl openssl-devel 

1. prce-devel 作用： nginx的rewrite伪静态匹配规则用到正则，pcre就用来干这个。
2. zlib 是一个压缩和解压缩模块
3.  SSL是Secure Socket Layer（安全套接层协议）的缩写，可以在Internet上提供秘密性传输
* **下载nginx tar包**
    ```
    // 创建一个文件夹
    cd /usr/local
    mkdir nginx
    cd nginx
    // 下载 tar包
    wget http://nginx.org/download/nginx-1.13.7.tar.gz
    tar -xvf nginx-1.13.7.tar.gz
    ```
* **安装nginx**
    ```
    //进入nginx目录 
    cd /usr/local/nginx/nginx-1.13.7
    //使用默认配置
    ./configure
    ```
    ```
    //make make install
    make
    make install
    ```
* 安装好后的目录如下
 ![image](http://182.61.41.64/images/1.jpg)
    * conf 目录下的 nginx.conf 是nginx的配置文件
    * sbin 目录下 执行 ./nginx 启动服务
* **一些常用命令**
    * 测试配置文件 /ningx/sbin/nginx -t
    * 启动命令 安装路径下的 /nginx/sbin/nginx
    * 停止命令 安装路径下的 /nginx/sbin/nginx -s stop
    * 重启命令 安装路径下的 .nginx/sbin/nginx -s reload
    * 查看进程命令 ps -ef | grep nginx
    * 平滑重启 kill -HUP Nginx主进程号
     
### Nginx 映射本地目录
***通过反向代理来做一个简易的图片服务器***

* 大致步骤：
    *  取消注释开启日志，便于出现问题排查
    ```
    error_log  logs/error.log;
    #error_log  logs/error.log  notice;
    #error_log  logs/error.log  info;

    ```
    * 修改 nginx/conf/nginx.conf, 存放图片的目录被我放在了 /root/pics    
    ```
    server {
        listen       80;
        server_name  182.61.41.64;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location ^~ /images/ {
            alias   /root/pics/;
            index   1.txt;
        }

    ```
* 过程当中踩到的坑：
    * 访问资源出现403 forbidden，查看日志出现错误：
    
    > 403 forbidden (13: Permission denied)  Nginx 403 forbidden forbidden 403 Permission denied
    
    原因是目录权限不够，解决办法： 修改web目录的读写权限，或者是把nginx的启动用户改成目录的所属用户，重启Nginx即可解决。

    **chmod -R 777 /data** <br/>
    **chmod -R 777 /data/www/**
    * 关于nginx root路径的一些说明：
        * nginx指定文件路径有两种方式root和alias，指令的使用方法和作用域：<br/>
        [root] <br/>
        语法：root path <br/>
        默认值：root html <br/>
        配置段：http、server、location、if
        * [alias] <br/>
        * 语法：alias path <br/>
        * 配置段：location
        
        root与alias主要区别——在于nginx如何解释location后面的uri，这会使两者分别以不同的方式将请求映射到服务器文件上。
        root的处理结果是：root路径＋location路径
        alias的处理结果是：使用alias路径替换location路径
        alias是一个目录别名的定义，root则是最上层目录的定义。
        还有一个重要的区别是alias后面必须要用“/”结束，否则会找不到文件的，而root则可有可无。

    ```
    //root实例：
    location ^~ /t/ {
         root /www/root/html/;
    }
    //如果一个请求的URI是/t/a.html时，web服务器将会返回服务器上的/www/root/html/t/a.html的文件。
    ```

    ```
    //alias实例：
    location ^~ /t/ {
         alias /www/root/html/new_t/;
    }
    //如果一个请求的URI是/t/a.html时，web服务器将会返回服务器上的/www/root/html/new_t/a.html的文件。注意这里是new_t，因为alias会把location后面配置的路径丢弃掉，把当前匹配到的目录指向到指定的目录。
    ```

    
##### 注意：
1. 使用alias时，目录名后面一定要加"/"。
2. alias在使用正则匹配时，必须捕捉要匹配的内容并在指定的内容处使用。
3. alias只能位于location块中。（root可以不放在location中）







date: 2018-08-06 20:15:48
tags:
---
