# openresty 编译安装及使用

## openresty 简介

- 个人描述:高级版 nginx(支持更多组件支持 lua 编程.
- 官方描述

  > OpenResty® 是一个基于 Nginx 与 Lua 的高性能 Web 平台，其内部集成了大量精良的 Lua 库、第三方模块以及大多数的依赖项。用于方便地搭建能够处理超高并发、扩展性极高的动态 Web 应用、Web 服务和动态网关。  
  > OpenResty® 通过汇聚各种设计精良的 Nginx 模块（主要由 OpenResty 团队自主开发），从而将 Nginx 有效地变成一个强大的通用 Web 应用平台。这样，Web 开发人员和系统工程师可以使用 Lua 脚本语言调动 Nginx 支持的各种 C 以及 Lua 模块，快速构造出足以胜任 10K 乃至 1000K 以上单机并发连接的高性能 Web 应用系统。  
  > OpenResty® 的目标是让你的 Web 服务直接跑在 Nginx 服务内部，充分利用 Nginx 的非阻塞 I/O 模型，不仅仅对 HTTP 客户端请求,甚至于对远程后端诸如 MySQL、PostgreSQL、Memcached 以及 Redis 等都进行一致的高性能响应。

## 使用方式

- 基本命令

```bash
# 启动
nginx
# 测试配置文件是否成功
nginx -t
# 平滑重启
nginx -s reload
```

## 安装过程

1. 安装 yum
   > `wget http://yum.baseurl.org/download/3.2/yum-3.2.28.tar.gz`  
   > `tar xvf yum-3.2.28.tar.gz`  
   > `cd yum-3.2.28`  
   > `yummain.py install yum`
2. 安装使用到的包
   > `yum install pcre-devel openssl-devel gcc curl`
3. 安装 perl

   ```bash
   yum install perl* (yum安装perl相关支持)
   yum install cpan (perl需要的程序库，需要cpan的支持，详细自行百度)
   wget http://www.cpan.org/src/5.0/perl-5.16.1.tar.gz
   tar -zxvf perl-5.16.1.tar.gz
   ./Configure -des -Dprefix=/usr/local/perl
   make
   make test
   make install
   ```

4. 下载源码并解压
   > `wget https://openresty.org/download/openresty-1.15.8.1rc1.tar.gz` > `tar -xzvf openresty-1.15.8.1rc1.tar.gz`
5. 编译配置文件
   > `cd openresty-1.15.8.1rc1/`  
   > `./configure //这一步完成后会输出安装位置及配置位置最好保存一下`
6. 手动创建目录
   > `cd /usr/local`  
   > `mkdir openresty`
7. 安装
   > `make`  
   > `sudo make install`
8. 上传静态文件(这里使用的是默认地址.
   > `cd /opt/openresty/nginx/html`  
   > `scp root@ip(使用各种方式上传文件到服务器)`
9. 修改配置文件(这里使用的是默认地址.
   > `cd /opt/openresty/nginx/conf`  
   > `vi nginx.conf`
10. 启动、测试、重启

```bash
#启动
cd /opt/openresty/nginx/sbin/
./nginx
# 测试
cd /opt/openresty/nginx/sbin/
./nginx -t
# 重启
cd /opt/openresty/nginx/sbin/
./nginx -s reload
```

## 配置文件

```bash
#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;

events {
    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;
    # 后端api转发
    server
    {
        #转发端口 待访问端口
        listen 5999;
        #转发域名 本地域名或者ip
        server_name 10.0.54.164;
        #转发路径
        location / {
            #转发头缓存大小
            proxy_buffer_size 64k;
            proxy_buffers   32 32k;
            proxy_busy_buffers_size 128k;
            #设置跨域访问
            more_set_headers 'Access-Control-Allow-Origin: $http_origin';
            #添加新的 在使用include模式的时候设置允许携带信息
            add_header 'Access-Control-Allow-Credentials' 'true';
            #被转发ip，端口隐藏
            proxy_redirect http://restapi.amap.com/v3/weather/weatherInfo http://10.0.54.164:5999/;
            #被转发地址
            proxy_pass   http://restapi.amap.com/v3/weather/weatherInfo;
            }
        #日志
        access_log logs/show.log;
    }
    # 前端静态文件部署
    server
    {
        #转发端口
        listen 8088;
        #转发域名
        server_name 10.0.54.164;
        #转发路径
        location / {
            # 静态文件地址
            root   html;
            # 首页查询顺序
            index  index.html index.htm;
            # 使用前端路由要配置默认访问地址为index.html
            try_files $uri $uri/ /index.html last;
            }
        #日志
        access_log logs/show.log;
    }

}

```

## 相关链接

[openresty 官方安装说明](http://openresty.org/cn/installation.html)  
[常见错误及处理方式](http://moguhu.com/article/detail?articleId=54)  
[yum 安装和更新源](https://www.cnblogs.com/kabi/p/5232420.html)  
[CentOS 下安装 yum](https://www.cnblogs.com/jukaiit/p/8877975.html)  
[CENTOS 7 安装 perl 环境](https://blog.csdn.net/zhang6622056/article/details/52594242)
