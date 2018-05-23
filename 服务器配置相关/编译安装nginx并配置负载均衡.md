### 编译安装nginx并配置负载均衡
1. 下载安装nginx
2. 解压nginx `tar -zxvf nginx-1.11.6.tar.gz`
3. 进入解压目录执行`./configure`（非阿里云需要自己安装gcc编译程序`yum install gcc-c++`）
4. 查看缺少的组件一般缺少（pcre，pcre-devel，zlib，zlib-devel，openssl ，openssl-devel）   
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


5. 默认安装位置

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

6. nginx 的负载均衡

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
     + 上面3种是nginx自带的负载均衡策略还可以自定义策略
     + 比如哈希一致性负载均衡策略 [哈希一致性负载均衡策略](https://blog.csdn.net/zhangskd/article/details/50256111)
     + 比如fair [fair下载](https://github.com/xyang0917/nginx-upstream-fair)
     + 比如url_hash [1.7.2版本之后nginx内置](https://github.com/evanmiller/nginx_upstream_hash) [url_hash详细说明](https://blog.csdn.net/wych1981/article/details/48544541)


7. nginx 的session共享 

     + 

8. 参考链接 
> [nginx在linux上的编译安装](https://blog.csdn.net/w410589502/article/details/70787468)   
[ngxin在mac上的编译安装](https://gist.github.com/Mioke/ae35fa333dee3b2ac137)     
[nginx常用配置](https://imququ.com/post/my-nginx-conf.html)  
[nginx+tomcat负载均衡配置](https://www.cnblogs.com/007sx/p/6917155.html)   
[nginx负载均衡一致性哈希](https://blog.csdn.net/zhangskd/article/details/50256111)  
[nginx负载均衡配置](https://blog.csdn.net/xyang81/article/details/51702900)   









