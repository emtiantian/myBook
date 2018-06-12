## linux常用命令  
### vim  
`vim xx.conf`              
>vim 打开文件  也可以作为新建

`esc + :q!`  
>不保存退出  

`esc +:wq` 
>保存退出  

`p`  
>粘贴剪贴板内容 

`/xxx ?xxx`  
>搜索  

`dd`  
>删除当前行  

`v + y`  
>v进入复制页面 y复制所选行  

`$`
>行末

`0`
>行首

`ctrl + G`
>显示当前行

`n+G`
>跳转到第n行    

### 文件相关
`stat`
>显示文件详细修改信息

`scp -v file root_name@ip:dir `
>远程传输本地文件到linux服务器

`tar -zxvf /tmp/etc.tar.gz`
>解压到程序压缩包到当前目录

### 查看系统版本
`lsb_release -a`
>显示当前linux 的系统信息

### 测试端口是否联通
`nc -zv ip port`
>测试当前ip对应端口tcp是否打开
`nc -uzv ip port`
>测试当前ip对应端口udp是否打开
`telnet ip prot`
>测试当前对应ip端口是否打开

### http访问
`curl -L http://xxxx`
>curl执行时遇到跳转 加-L参数

### 定时任务
`crontab -e`
>编辑命令或者执行命令脚本    

格式示例:   

    # Example of job definition: 　　　　　　　　　#  计划任务书写格式
    # .---------------- minute (0 - 59)   　　
    # |  .------------- hour (0 - 23)
    # |  |  .---------- day of month (1 - 31)
    # |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
    # |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
    # |  |  |  |  |
    # *  *  *  *  * user-name command to be executed

` 0 23 * * * /opt/gitlab/bin/gitlab-rake gitlab:backup:create >> /var/opt/gitlab/backups/backup.log 2>&1`

### 设置路由规则 
对双网卡上网的linux机器除了设置2个ip和一个网关外 还需要配置路由表
命令有2种 net-tools系列 和 iproute2系列 推荐使用 iproute2 系列命令（linux发行版基本预装过）

`ip route`








