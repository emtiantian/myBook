## nginx配置说明
1. 安装nginx
2. 修改nginx配置文件
>
        server
        {
            #转发端口
            listen 8002;
            #转发域名
            server_name localhost;
            #转发路径
            location / {
                #转发头缓存大小
                proxy_buffer_size 64k;  
                proxy_buffers   32 32k;  
                proxy_busy_buffers_size 128k;
                #被转发ip，端口隐藏
                proxy_redirect http://10.168.1.219/ http://mistest.windey.cn/;
                #被转发地址
                proxy_pass   http://10.168.1.219/;
                }
            #日志
            access_log logs/show.log;
        }

3. 基本操作
> windows 
> 开始 nginx.exe
> 停止 nginx.exe -s stop
> 重新加载配置文件启动 nginx.exe -s reload
> linux
> 开始 nginx
> 停止 nginx -s stop 
> 重新加载配置文件启动 nginx -s reload 
> 注： 可能需要管理员权限
