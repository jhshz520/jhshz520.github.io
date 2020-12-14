---
title: docker实战
date: 2019-11-21 20:30:34
categories: docker
tags: docker
---
# windows 下安装DockerToolbox

# Linux 下安装docker-compose
1. 下载
```
#sudo curl -L "https://github.com/docker/compose/releases/download/1.25.0/docker-compose-$(uname -s)-$(uname -m)"

#-o /usr/local/bin/docker-compose
```
2. 改变执行权限
```
#sudo chmod +x /usr/local/bin/docker-compose**
```
3. 最重要的一步添加软连接，因为路径中不包含usr/local/bin
因此添加以下语句

sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose

# docker容器管理

```列出所有的容器 ID

docker ps -aq
停止所有的容器

docker stop $(docker ps -aq)
删除所有的容器

docker rm $(docker ps -aq)
删除所有的镜像

docker rmi $(docker images -q)
复制文件

docker cp mycontainer:/opt/file.txt /opt/local/docker cp /opt/local/file.txt mycontainer:/opt/

更新 : @snakeliwei 的提醒， 现在的docker有了专门清理资源(container、image、网络)的命令。 docker 1.13 中增加了docker system prune的命令，针对container、image可以使用docker container prune、docker image prune命令。

docker image prune --force --all或者docker image prune -f -a` : 删除所有不使用的镜像

docker container prune: 删除所有停止的容器
```
## 容器于镜像的关系？

```

docker container ls --all
docker start containerID/Names # 启动容器
docker stop containerID/Names  # 停止容器
docker rm containerID/Names    # 删除容器
docker logs containerID/Names  # 查看日志
docker exec -it containerID/Names /bin/bash  # 进入容器

# 从正在运行的 Docker 容器里面，将文件拷贝到本机，注意后面有个【点】拷贝到当前目录
docker container cp containID:/path/to/file].

docker run centos echo "hello world"  # 在docker容器中运行hello world!
docker run centos yum install -y wget # 在docker容器中，安装wget软件
docker ps                           # 列出包括未运行的容器
docker ps -a                        # 查看所有容器(包括正在运行和已停止的)
docker logs my-nginx                # 查看 my-nginx 容器日志

# docker run -i -t centos /bin/bash   # 启动一个容器
docker inspect centos     # 检查运行中的镜像
docker commit 8bd centos  # 保存对容器的修改
docker commit -m "n changed" my-nginx my-nginx-image # 使用已经存在的容器创建一个镜像
docker inspect -f {{.State.Pid}} 44fc0f0582d9 # 获取id为 44fc0f0582d9 的PID进程编号
# 下载指定版本容器镜像
docker pull gitlab/gitlab-ce:11.2.3-ce.0
## dockr 操作

docker images

列出本地主机的镜像

sudo docker run -t -i ubuntu：14.04 /bin/bash

运行一个带标签镜像的容器

sudo docker pull centos

获取一个新的镜像

sudo docker search sinatra

查找镜像


更新并提交更改

sudo docker run -t -i traing/sinatra/bin/bash

在运行容器内使用gem 来安装json

gem install json

输入exit退出容器

$ sudo docker commit -m="Added json gem" -a="Kate Smith"\
0b2616b0e5a8 ouruser/sinatra:v2
4f177bd27a9ff0f6dc2a830403925b5360bfe0b93d476f7fc3231110e7f71b1c

-m 是更新的信息，a是作者信息

Dockerfile 创建镜像
makdir sinatra
cd sinatra
touch Dockerfile


```




