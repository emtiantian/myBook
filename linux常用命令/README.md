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
 
`ip route show` 或 `ip route`
>列出当前路由表

`ip addr add 192.168.0.193/24 dev wlan0`
>为 wlan0 设置ip为 192.168.0.193 子网掩码为 24（255.255.255.0）   
>删除示例: ip addr del 192.168.0.193/24 dev wlan0 

`sudo ip route add 172.1.25.0/24 via 172.1.27.254 dev em1`
`sudo ip route add 10.168.1.0/24 via 192.168.100.254 dev em2`
>为 172.1.25网段设置 网关为 172.1.27.254  默认出口为em1

[ip route 详细说明](http://www.mamicode.com/info-detail-1412618.html)   
[ip route 高级设置](https://www.cnblogs.com/taosim/articles/4444887.html)

### 修改文件或文件夹权限

`chmod 777`
>修改 第一个7:文件拥有者 第2个7:同组用户 第三个7:其他人访问权限   
>单独一个7 其实是二进制 111 的表述    
>第一个1：读文件或读取文件夹目录    
>第二个1：写权限新增修改文件或删除移动文件夹   
>第三个1：执行文件权限或进入文件夹权限

### 修改文件夹或文件拥有者
`chown`
>修改文件或者文件夹的拥有者
>chown -R admin /opt/nginx
 
+ user : 新的档案拥有者的使用者 ID
+ group : 新的档案拥有者的使用者群体(group)
+ -c : 若该档案拥有者确实已经更改，才显示其更改动作
+ -f : 若该档案拥有者无法被更改也不要显示错误讯息
+ -h : 只对于连结(link)进行变更，而非该 link 真正指向的档案
+ -v : 显示拥有者变更的详细资料
+ -R : 对目前目录下的所有档案与子目录进行相同的拥有者变更(即以递回的方式逐个变更)
+ –help : 显示辅助说明
+ –version : 显示版本
+ 

### 查看硬盘使用情况和文件夹大小

#### 查看磁盘用量
1. `df -lh `
2. <p style="color:red"> 如果linux中df和du命令对比出来的磁盘大小不一致，</p>
<p style="color:red">估计是删除文件但是进程没有销毁文件仍然存在，需要重启系统可以解决</p>

#### 查看文件夹大小并排序
1. `du -s * | sort -nr  ` 
2. `du -s * | sort -nr  | head`  取前10个

### ssh 免密登录

`scp ./id_rsa.pub root@A服务器ip:~/.ssh/id_rsa.pub_temp` 
>将生成的公钥文件id_rsa.pub复制到A服务器上，并改名（避免覆盖A服务器上的公钥）   
     
`cat id_rsa.pub_temp >> authorized_keys `
>将刚才复制过来的公钥信息追加到文件authorized_keys文件（追加的意义是避免覆盖其他公钥信息，如果没有这个文件系统会自动创建） 

### 查看进程及端口占用
1. `ps -ef | grep java` 查看进程占用
2. `netstat –apn` 查看进程占用端口










