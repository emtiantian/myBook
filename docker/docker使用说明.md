## docker使用说明
1. `docker pull` 拉取docker hub 上的镜像
2. `docker images` 查看当前镜像
3. `docker rm` `docker rmi` `docker rm $(docker ps -aq)`  
> 删除 容器，删除镜像，删除全部容器
4. `docker ps -a` 查看当前全部容器
5. `docker stop <实例名称/实例id>` 停止当前运行的实例