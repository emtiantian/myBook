## 编译安装 nginx 并配置负载均衡

1.  下载安装 nginx
2.  解压 nginx `tar -zxvf nginx-1.11.6.tar.gz`
3.  进入解压目录执行`./configure`（非阿里云需要自己安装 gcc 编译程序`yum install gcc-c++`）
4.  查看缺少的组件一般缺少（pcre，pcre-devel，zlib，zlib-devel，openssl ，openssl-devel）  
    安装示例：`yum install -y openssl openssl-devel`（阿里云上的安装方式）  
    或者源码编译安装，安装示例：

          ./configure --prefix=/usr/local/nginx --with-zlib=../zlib-1.2.8 --with-pcre=../pcre-8.36
          make
          sudo make install
          #如果新加模块
          ./configure –add-module=/data/software/ngx_http_google_filter_module
          make 自动替换原来的nginx二进制
          #查看已经编译的模块 V是大写的
          nginx -V

5)  默认安装位置

          nginx path prefix: "/usr/local/nginx"
          nginx binary file: "/usr/local/nginx/sbin/nginx"
          nginx modules path: "/usr/local/nginx/modules"
          nginx configuration prefix: "/usr/local/nginx/conf"
          nginx configuration file: "/usr/local/nginx/conf/nginx.conf"
          nginx pid file: "/usr/local/nginx/logs/nginx.pid"
          nginx error log file: "/usr/local/nginx/logs/error.log"
          nginx http access log file: "/usr/local/nginx/logs/access.log"
          nginx http client request body temporary files: "client_body_temp"
          nginx http proxy temporary files: "proxy_temp"
          nginx http fastcgi temporary files: "fastcgi_temp"
          nginx http uwsgi temporary files: "uwsgi_temp"
          nginx http scgi temporary files: "scgi_temp"

6)  nginx 的负载均衡

          upstream  mis-tomcat {
             #负载均衡配置为轮换分配
             #round-robin;
             #负载均衡配置为哪个链接少就分配到哪个
             #least_connected;
             # 集群mistomcat 配置
             # weight 权重相同
             # max_fails 最大失败次数
             # fail_timeout 每次超过最大失败次数停用时间
             # backup 所有其他服务器都挂了的话backup生效
             # down 服务器不启用
             # resolve 指定域名解析服务
             server 10.168.1.218:9080  weight=1 max_fails=1 fail_timeout=10;
             server 10.168.1.218:10080 weight=1;
             server 10.168.1.219:8080 backup down resolve
             #负载均衡配置为根据ip值绑定，但是服务器不可用就会切换机器
             ip_hash;
          }

    - 上面 3 种是 nginx 自带的负载均衡策略还可以自定义策略
    - 比如哈希一致性负载均衡策略 [哈希一致性负载均衡策略](https://blog.csdn.net/zhangskd/article/details/50256111)
    - 比如 fair [fair 下载](https://github.com/xyang0917/nginx-upstream-fair)
    - 比如 url_hash [1.7.2 版本之后 nginx 内置](https://github.com/evanmiller/nginx_upstream_hash) [url_hash 详细说明](https://blog.csdn.net/wych1981/article/details/48544541)

7. nginx 的 session 共享

   - 使用 cookie 储存 session（代码部分配置和修改）（而且不安全 cookie 会被伪造）
   - 使用数据库储存 session (代码改动)（加大了数据库负担，切同步时间较慢）
   - 使用 memcache 或 redis (代码改动)（速度快，但是有内存压力，内存不足会溢出）
   - nginx 的 ip_hash (nginx 改动) （使用 ip 做 hash 来分配用户到固定机器）
     > 有 3 个弊端:  
     > nginx 服务器要在最前端能够获取真实 ip,  
     > nginx 分发之后不能再次分发否则 session 不起作用,  
     > 如果大量用户在同一局域网访问负载均衡失效
   - upstream_hash (nginx 改动)（第三方模块）（和 ip_hash 差不多只是更改做 hash 的参数）

8. 参考链接
   > [nginx 在 linux 上的编译安装](https://blog.csdn.net/w410589502/article/details/70787468)  
   > [ngxin 在 mac 上的编译安装](https://gist.github.com/Mioke/ae35fa333dee3b2ac137)  
   > [nginx 常用配置](https://imququ.com/post/my-nginx-conf.html)  
   > [nginx+tomcat 负载均衡配置](https://www.cnblogs.com/007sx/p/6917155.html)  
   > [nginx 负载均衡一致性哈希](https://blog.csdn.net/zhangskd/article/details/50256111)  
   > [nginx 负载均衡配置](https://blog.csdn.net/xyang81/article/details/51702900)  
   > [nginx 和 apache 的比较](https://www.oschina.net/question/565065_77181) > [nginx 的 session 共享问题](http://blog.51cto.com/815632410/1569828)
