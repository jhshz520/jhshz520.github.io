---
title: 第一本docker书2
date: 2020-11-23 08:39:10
categories: docker
tags: docker
---
# docker 容器镜像和镜像仓库
## 列出镜像
```
docker images
```
<font color='yellow'>
本地镜像都保存在 /var/lib/docker 目录下，每个镜像保存在所用的存储驱动的目录下如aufs或者devicemapper
</font>

## 查找镜像
```
docker search puppet NAME
```

## docker pull
```
sudo docker pull jamtur01/puppetmaster


sudo docker run -i -t jamtur01/puppetmaster /bin/bash 
```
利用镜像创建一个容器

## 构建镜像
docker build 命令和 dockerfile文件

## 登录到docker hub

```
sudo docker login

sudo docker logout
```
<font color='yellow'>
个人的认证信息会保存到 $HOME/.dockercfg文件

1.7.0 之后
$HOME/.docker/config.json
</font>

## docker commit
创建一个要进行修改的定制容器
```
sudo docker run -i -t ubuntu /bin/bash 

apt-get -yqq update

apt-get -y install apache2

sudo docker commit 原容器id jamtur01/apache2
```

-a 作者

-m "进行改动的信息描述"

:要创建的目标镜像的标签名

提交保存时，智能选用那个正在运行的容器，在制作特定镜像时，直接使用cmmit命令只是一个临时性的辅助命令，不推荐使用，官方建议使用docker build 命令结合dockerfile文件创建和管理镜像

## docker history

events history logs 用于查看docker的系统的日志信息

docker history 跟的参数是iamge的名称或者id，然后就会显示镜像的每一层，以及创建这些层的指令

```
docker events [options]

docker historys [options] image

docker logs [options] container
```


## 启动分离式容器
```
sudo docker run -d -p 80 --name static_web jamtur01/static_web \nginx -g  "daemon off;"
```

<font color='yellow'>
-p 标志用来控制公开的网络端口号给外部，docker run 命令会随机选择一个 32768~61000的一个大的端口号来映射到容器的80 端口
</font>
## 查看docker 端口的映射情况
docker ps -l
## docker port 
```
docker port 容器id 80
```
这样就能够查看该容器id的80端口所对应的宿主机的端口号

## 通过-p 命令映射特定的端口号

```
sudo docker run -d -p 80：80 --name static_web jamtur01/static_web \nginx -g  "daemon off;"
```
第一个80代表宿主机的80，第二个80代表容器的80 端口