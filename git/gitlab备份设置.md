## gitlab 备份设置

1.  设置 gitlab 备份保存时间  
    `vim /etc/gitlab/gitlab.rb`

                gitlab_rails['backup_keep_time'] = 86400
                #修改备份保存时间为1天

2.  创建定时任务  
    `crontab -e`

                # 备份每天23点备份并写入log日志
                0 23 * * * /opt/gitlab/bin/gitlab-rake gitlab:backup:create >> /var/opt/gitlab/backups/backup.log 2>&1

3.  修改备份目录权限 （默认路径：`/var/opt/gitlab/backups`）  
    （<span style="color: red">设置了也没有用,需要用 root 用户登录</span>）  
     `cd /var/opt/gitlab/`  
     `chmod 777 backups/`

4) 备份服务器创建读取任务 (这里使`scp`命令)
   - 备份服务器为 windows（我这里下载使用 gitbash）
   - 创建 ssh 秘钥 `ssh-keygen -t rsa`(创建 ssh-key 的时候不要创建密码)
   - 添加公钥到服务器的 authorzed_keys(现在可以免密传输读取服务器文件了)
   - 编写远程命令`scp username@ip:/var/opt/gitlab/backups/* c:/git-backup/`
   - 添加到定时任务
