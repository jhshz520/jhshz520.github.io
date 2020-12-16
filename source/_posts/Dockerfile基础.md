---
title: Dockerfile基础
date: 2020-01-17 09:19:14
categories: Docker
tags: docker
---
# Dockerfile的语法

dockerfile 基于DSL（domain specific language）语法，更具备可重复性，透明性和幂等性
## FROM
基础镜像，当前的镜像是基于哪个镜像的
## MAINTAINER
镜像维护者的姓名和邮箱地址
## EXPOSE
当前容器对外暴露出的端口
## RUN
**容器构建**时的运行命令,相当于在容器执行一个命令行，RUN有两种格式

shell格式
```
RUN <命令行命令>
# <命令行命令> 等同于，在终端操作的 shell 命令。
```

exec格式
```
RUN ["可执行文件", "参数1", "参数2"]
# 例如：
# RUN ["./test.php", "dev", "offline"] 等价于 RUN ./test.php dev offline
```

<font color='yellow'>

dockerfile的指令每执行一次就会在docker上新建一层，过多的无意义层会造成镜像膨胀过大，所以不要过多运行多行run
</font>
例如
```
FROM centos
RUN yum install wget
RUN wget -O redis.tar.gz "http://download.redis.io/releases/redis-5.0.3.tar.gz"
RUN tar -xvf redis.tar.gz
以上执行会创建 3 层镜像。可简化为以下格式：
FROM centos
RUN yum install wget \
    && wget -O redis.tar.gz "http://download.redis.io/releases/redis-5.0.3.tar.gz" \
    && tar -xvf redis.tar.gz
```



## WORKDIR
指定容器创建后，终端默认登录进来的工作目录一个落脚点
## ENV 
用来构建镜像过程中设置环境变量
## ADD
将宿主机目录下的文件拷贝进镜像，且自动处理URL和解压tar包


ADD 指令和 COPY 的使用格式一致（同样需求下，官方推荐使用 COPY）。功能也类似，不同之处如下：

ADD 的优点：在执行 <源文件> 为 tar 压缩文件的话，压缩格式为 gzip, bzip2 以及 xz 的情况下，会自动复制并解压到 <目标路径>。

ADD 的缺点：在不解压的前提下，无法复制 tar 压缩文件。会令镜像构建缓存失效，从而可能会令镜像构建变得比较缓慢。具体是否使用，可以根据是否需要自动解压来决定。

## COPY
将从构建上下文目录的原路径文件复制到新一层的镜像内的<目标路径位置>
从工程目录中copy到镜像中

格式：
```
COPY [--chown=<user>:<group>] <源路径1>...  <目标路径>
COPY [--chown=<user>:<group>] ["<源路径1>",...  "<目标路径>"]

[--chown=<user>:<group>]：可选参数，用户改变复制到容器内文件的拥有者和属组。


COPY hom* /mydir/
COPY hom?.txt /mydir/
```
## VOLUME
容器数据卷，用于数据保存和持久化工作
## CMD

类似于RUN指令，用于**运行程序**，但是两者的时间点不同

CMD是在docker run时运行

RUN是在docker build时运行

<font color='yellow'>

如果dockerfile中存在多个CMD指令，仅最后一个生效
</font>

## ENTERPOINT
与CMD大致相同，但CMD只有最后一个命令会生效
ENTERPOINT 则是追加模式

不会被docker run 的命令行参数指定的指令覆盖，并且这些命令行参数会当做参数送给ENTRYPOINT指令指定的程序

如果dockerfile中存在多个enterypoint指令，仅最后一个生效，经常搭配CMD命令使用，变参使用CMD，CMD相当于给ENTRYPOINT 传参
例子
```
FROM nginx

ENTRYPOINT ["nginx", "-c"] # 定参
CMD ["/etc/nginx/nginx.conf"] # 变参 
```

假设通过以上的操作构建了 nginx:test 镜像

不传参运行
```
docker run nginx:test
```

相当于容器内默认运行以下命令，启动主进程

```
nginx -c /etc/nginx/nginx.cnf

```

传参运行
```
docker run nginx:test -c /etc/nginx/new.conf

```

容器会默认运行以下命令
```
nginx -c /etc/nginx/new.conf
```


## ONBUILD
当构建一个被继承的Dockerfile时运行命令，父镜像在被子镜像继承后父镜像的onbuild被触发

用于**延迟构建命令**的执行。简单的说，就是 Dockerfile 里用 ONBUILD 指定的命令，在本次构建镜像的过程中不会执行（假设镜像为 test-build）。当有新的 Dockerfile 使用了之前构建的镜像 FROM test-build ，这是执行新镜像的 Dockerfile 构建时候，会执行 test-build 的 Dockerfile 里的 ONBUILD 指定的命令。


# 项目文件中的dockerfile
```

```

## 
```
<!--{% load user_tags %} -->

保留到html页面中依然会出错
```

## ARG
ARG是运行时的参数，ENV是全局参数

一般用于build时候的使用

## lable指定元数据
例子

```
LABEL k='v' k1='v1'

```
便于后续inspect查看是否满足某些标识
