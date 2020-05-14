# https静态容器部署（单向及双向证书认证）

``` conf
    #HTTPS server
     server {
        listen       22000 ssl;
        server_name  10.0.2.89;

        #ssl_certificate      ./ssl/server.crt; //单向认证
        #ssl_certificate_key  ./ssl/server.key; //单向认证
        # start
        ssl_certificate      ./ssl/my_key_store.crt;
        ssl_certificate_key   ./ssl/my_store.key;
        #ssl_client_certificate ssl/ca2/ca.crt; //双向认证
        #ssl_verify_client on; //双向认证
        # end

        #    ssl_session_cache    shared:SSL:1m;
        #    ssl_session_timeout  5m;

        #    ssl_ciphers  HIGH:!aNULL:!MD5;
        #    ssl_prefer_server_ciphers  on;

        location / {
            root   /root/iothub/iothub-front;
            index  index.html index.htm;
            try_files $uri $uri/ /index.html last;
        }
    }
    server
    {
        #转发端口
        listen 22000;
        #转发域名
        server_name 10.0.2.89;
        #转发路径
        location / {
            root   /root/iothub/iothub-front;
            index  index.html index.htm;
            try_files $uri $uri/ /index.html last;
            }
        #日志
        #access_log logs/show.log;
    }
    # nginx 转发ws协议
    server
    {
        #转发端口
        listen 22000;
        #转发域名
        server_name 10.0.2.71;
        #转发路径
        location /{
            proxy_pass https://10.0.2.123:18080;
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "upgrade";
            }
        #日志
        #access_log logs/show.log;
    }
    # nginx支持多项目同域名部署 前端需要修改添加统一前缀 如authui
    # vue工程中index.html静态文件引入加<%= BASE_URL %>
    # vue.config.js中publicPath='/authui/'  router中添加base:'/authui/'
    # nginx 创建 authui文件夹放置项目文件
    server
    {
        #转发端口
        listen 22000;
        #转发域名
        server_name 10.0.0.19;
        #转发路径
        location ^~ /authui {
            root   html;
            index  index.html index.htm;
            try_files $uri $uri/ /authui/index.html last; # 配置地址原则是 root（基础地址） +/authui/index.html 为绝对地址
            }
        #日志
        #access_log logs/show.log;
    }
    ```
