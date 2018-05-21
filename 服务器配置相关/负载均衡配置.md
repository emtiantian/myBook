[TOC]


##负载均衡配置


1. 下载安装，apache服务器，下载安装jk_mod模块（放置在apache/mod下面）
>apache 下载 [https://httpd.apache.org/download.cgi](link)   
jk_mod 下载 [https://tomcat.apache.org/download-connectors.cgi](link)
2. 修改apache配置
    - 2.1 修改httpd.conf
        - 2.1.1 启用下述模块  
        > mod_proxy.so
          mod_proxy_ajp.so   
          mod_proxy_balancer.so   
          mod_proxy_connect.so   
          mod_proxy_express.so  
          mod_slotmem_shm.so      
        - 2.1.2文件末尾添加如下配置   
        >       include conf/mod_jk.conf          
                #ProxyRequests Off      
                ProxyPass / balancer://proxy/ stickysession=JSESSIONID    lbmethod=byrequests     
                # ProxyPassReverse / balancer://proxy/   
                <Proxy balancer://proxy>    
                  BalancerMember ajp://localhost:8009/ loadfactor=1 route=server1  
                  BalancerMember ajp://localhost:9009/ loadfactor=1 route=server2  
                #热备   BalancerMember ajp://10.88.8.137:10002/ loadfactor=1 route=61b status=+H  
                </Proxy>
        >解释:   
        >`ProxyPass / balancer://proxy/`         
            ProxyPass / 表示所有以 “/” 开头的请求都适配proxy这个balance策略，`/`和`proxy`的部分是参数，是可变的，也就是说可以给不同的请求url匹配不同的均衡策略。   
        >`stickysession=JSESSIONIDlbmethod=byrequests`   
            stickysession是说请求是否是session黏贴性的，就是当第一次请求负载后，以后的请求会按照session走，既同一个session的请求都访问一个webserver（tomcat），如果是PHP实现的后台服务，JSESESIONID需要变成PHPSESSIONID,lbmethod后的参数还可以是bybusyness、bytraffic  
        >`BalancerMember ajp://10.88.8.137:10001/ loadfactor=1 route=61a`   
            这个配置是匹配不同的tomca，我这里使用3个tomcat，需要注意的是使用ajp协议（Apache使用这个协议实现的代理），端口是tomcat中ajp配置的端口 ，loadfactor后面的值是权值，负载均衡的时候会根据这个权值去分配（1-100）。Route是一个命名，如果仔细查看sessionid的话，你能看的这个路由信息会跟在sessionid后面。
        >`Status=+H`  
            表示10.88.8.137:10002这台server是热备服务器，正常业务不会分给它，但是一旦另外两台服务器down了，这台服务器就会被使用到。  
        - 2.1.3 修改apache部署位置说明
        >`Define SRVROOT "D:\public\mistest\Apache24"`  
        解释 双引号中的内容为部署apache的绝对位置
        - 2.1.4 修改servername
        >`ServerName 127.0.0.1:80`   
        修改为正确的ip和端口号
        
    - 2.2 添加配置文件
        - 2.2.1 添加jk_mod配置（新建）
        >       #加载mod_jk Module   注意so名称和下载引用的模块一致
                LoadModule jk_module modules/mod_jk-apache-2.0.55.so        
                #指定 workers.properties文件路径     
                JkWorkersFile conf/workers.properties         
                #指定那些请求交给tomcat处理,"controller"为在workers.propertise里指定的负载分配控制器       
                JkMount /*.jsp controller   
                #如果还要指定*.do也进行分流就再加一行
                #JkMount /*.do controller
                #如果你想对所有的请求进行分流只需要写成
                #JkMount /* controller
        - 2.2.2 添加workers.properties配置（新建）
        >       worker.list = controller,tomcat1,tomcat2  #server 列表      
                #========tomcat1========      
                worker.tomcat1.port=8019       #ajp13 端口号，在tomcat下server.xml配置,默认8009      
                worker.tomcat1.host=localhost  #tomcat的主机地址，如不为本机，请填写ip地址      
                worker.tomcat1.type=ajp13      
                worker.tomcat1.lbfactor = 1    #server的加权比重，值越高，分得的请求越多      
                #========tomcat2========      
                worker.tomcat2.port=8029       #ajp13 端口号，在tomcat下server.xml配置,默认8009      
                worker.tomcat2.host=localhost  #tomcat的主机地址，如不为本机，请填写ip地址      
                worker.tomcat2.type=ajp13      
                worker.tomcat2.lbfactor = 2    #server的加权比重，值越高，分得的请求越多            
                #========controller,负载均衡控制器========      
                worker.controller.type=lb      
                worker.controller.balanced_workers=tomcat1,tomcat2   #指定分担请求的tomcat      
                worker.controller.sticky_session=1   

3. 修改多个tomcat的配置
>如果你是水平集群，即在不同电脑上安装tomcat,tomcat的安装数量为一个，可以不必修改tomcat配置文件.  
>如果是垂直集群方式，即在同一个电脑上安装多个tomcat,tomcat的安装数量为多个，必须修改其中的设置，以避免端口冲突。   
例如把原来以8开头的端口号改为以9开头端口号。  
    - 3.1 修改tomcat2/conf/server.xml
    >`<Engine jvmRoute= "server1">`   
    修改tomcat的server.xml确定每个tomcat的名称（Engine下jvmRoute字段值目前用的是server1-5）  
    >tomcat端口三处（Server、Connector、redirectPort）确保在垂直部署的时候不重复，水平部署不需要修改  
    - 3.2 修改tomcat项目配置的web.xml
    >`<distributable/>`  
    添加在web.xml的最后一个标签`</web-app>`之前,用于session共享，集群中的每一个应用都要修改

    

4. apache 启动方法
> `cd  C：\MAS\TRSMAS\win31\apache\bin`   
> 进入安装目录
> `httpd.exe -k install`
> 注册apache服务
> `httpd.exe -k start`
> 启动服务

5. 常见错误
    - 5.1 tomcat错误
        - 1.tomcat 闪退 
        >可能是jdk和tomcat版本不兼容  
        可能是tomcat中项目配置有问题下一个新的tomcat启动试试
        可能是java_home配置有问题 删除所有不用的jdk版本在控制台中 输入`java -version`查看是否是需要版本
        - 2.apache启动错误
        >错误的套接字  
        是对应端口被占用
        >xxx.so is loaded ??   
        是对应的模块没有加载

6. 相关链接  
[http://www.cnblogs.com/jay36/p/7519901.html](link)  
[http://www.jb51.net/softjc/158436.html](link)


        

        











            


    