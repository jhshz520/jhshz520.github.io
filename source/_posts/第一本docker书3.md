---
title: 第一本docker书3
date: 2020-11-23 08:39:31
categories: docker
tags: docker
---
# docker容器的数据持久化

## 容器数据卷

## 从容器内拷贝文件到主机
**docker cp**

需要容器内的数据持久化
```
sudo docker cp 容器ID:容器内路径 目的主机路径

docker cp centos:/tmp/yum.log  /root

```

## docker run -it -v 挂载

```
sudo docker run -it -v /宿主机绝对路径目录:/容器内目录 镜像名
```
1. 查看数据卷是否挂载成功
2. 容器和宿主机进行数据共享
3. 容器退出后，主机进行了修改，重新启动容器，仍然会有相关改动，数据仍然同步
4. 权限问题，ro 只读权限，宿主机能够更改文件，容器只能查看，但是不能修改主机的文件
   
```
sudo docker run -it -v /宿主机绝对路径目录:/容器内目录  ro 镜像名
```
## dockerfile添加容器数据卷
volume指令
例子
```
FROM centos

VOLUME ["/dataVolumeContainer1","/dataVolumeContainer2"]

CMD echo "finished,----------success"
CMD /bin/bash
```

在宿主机执行 docker build -f /mydocker/Dockerfile -t zzyy/centos .

主机默认地址

如果是用dockerfile生成默认都是挂载在var/lib/docker/volumes/对应的的容器id/_data下

<font color="red">
如果docker挂载主机目录docker 访问出现 cannot open directory ：Permission denied

解决办法： 在挂载目录后加--previleged=true参数即可

</font>

## 数据卷容器

命名的容器挂载数据卷，其他容器通过挂载这个（父容器）实现数据共享，挂载数据卷容器，称为数据卷容器

**volumefrom**

```
sudo docker run -it --name dc01 zzyy/centos

sudo docker run -it --name dc02 --volume-from dc01 zzyy/centos

sudo docker run -it --name dc03 --volume-from dc01 zzyy/centos


```
dc01 和dc02 和 dc03 里面的挂载容器都是共享的，容器之间配置信息的传递，数据卷的生命周期一直持续到没有容器使用它为止