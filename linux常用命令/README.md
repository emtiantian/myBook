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


