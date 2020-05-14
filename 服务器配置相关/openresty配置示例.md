# openresty 配置示例

```config
server
    {
        #转发端口
        listen port1;
        #转发域名
        server_name ip1;
        #转发路径
        location / {
            #option转发控制
            if ( $request_method = 'OPTIONS' ) {
                add_header Access-Control-Allow-Origin $http_origin;
                add_header Access-Control-Allow-Headers Authorization,Content-Type,Accept,Origin,User-Agent,DNT,Cache-Control,X-Mx-ReqToken,X-Data-Type,X-Requested-With,username;
                add_header Access-Control-Allow-Methods GET,POST,OPTIONS,HEAD,PUT;
                add_header Access-Control-Allow-Credentials true;
                add_header Access-Control-Allow-Headers X-Data-Type,X-Auth-Token;
                # 到底是206还是204
                return 206;
            }
            if ( $request_method != 'OPTIONS' ) {
                #如果响应代码等于200、201（1.3.10），204、206、301、302、303、304、307（1.1.16、1.0.13）或308（1.13），则将指定字段添加到响应标头。参数值可以包含变量。
                # 如果添加always 则无论响应代码如何，都将添加头部字段。
                #add_header Access-Control-Allow-Origin $http_origin always;
                #add_header Access-Control-Allow-Credentials true;
                more_set_headers   'Access-Control-Allow-Origin: $http_origin';
                more_set_headers   'Access-Control-Allow-Credentials: true';
            }
            #忽略转发头 iframe限制
            proxy_hide_header X-Frame-Options;
            #转发头缓存大小
            proxy_buffer_size 128k;
            proxy_buffers   64 64k;
            proxy_busy_buffers_size 256k;
            #被转发ip，端口隐藏
            proxy_redirect http://ip:port/ http://ip1:port1/;
            #被转发地址
            proxy_pass   http://ip:port/;
            #try_files $uri $uri/ /index.html last;
            }
        #日志
        access_log logs/show.log;
    }
```
