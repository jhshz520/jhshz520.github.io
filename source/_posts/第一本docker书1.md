---
title: 第一本docker书1
date: 2020-11-23 08:38:46
categories: docker
tags: docker
---
# docker 容器生命周期
创建->管理->停止->删除

## dcoker info
```
Client:
 Debug Mode: false

Server:
 Containers: 3
  Running: 0
  Paused: 0
  Stopped: 3
 Images: 4
 Server Version: 19.03.12
 Storage Driver: overlay2
  Backing Filesystem: extfs
  Supports d_type: true
  Native Overlay Diff: true
 Logging Driver: json-file
 Cgroup Driver: cgroupfs
 Plugins:
  Volume: local
  Network: bridge host ipvlan macvlan null overlay
  Log: awslogs fluentd gcplogs gelf journald json-file local logentries splunk syslog
 Swarm: inactive
 Runtimes: runc
 Default Runtime: runc
 Init Binary: docker-init
 containerd version: 7ad184331fa3e55e52b890ea95e65ba581ae3429
 runc version: dc9208a3303feef5b3839f4323d9beb36df0a9dd
 init version: fec3683
 Security Options:
  apparmor
  seccomp
   Profile: default
 Kernel Version: 4.4.0-194-generic
 Operating System: Ubuntu 16.04.4 LTS
 OSType: linux
 Architecture: x86_64
 CPUs: 1
 Total Memory: 1.936GiB
 Name: migration_test
 ID: VBUT:IG4E:L3QT:SHCW:VEGP:XY72:VGPC:YJVB:VDUM:SXIH:UWWQ:NQZQ
 Docker Root Dir: /var/lib/docker
 Debug Mode: false
 Registry: https://index.docker.io/v1/
 Labels:
 Experimental: true
 Insecure Registries:
  127.0.0.0/8
 Registry Mirrors:
  https://bkry7tdy.mirror.aliyuncs.com/
 Live Restore Enabled: false

WARNING: No swap limit support
```

## docker run
```
-i 标志保证容器中的STDIN 是开启的

-t  要为创建的容器分配一个伪tty终端，保证容器提供一个交互式的shell

更多可查看docker help run
```
### 使用第一个容器

```
hostname
```

## docker help

## docker ps
列出所有的容器，默认情况下只能看到正在运行的容器，如果指定-a标志，会列出所有容器

-l 列出最后一个运行的容器，可以通过--format标志来进一步控制显示的信息

<font color='yellow'>

三种方式可以唯一的指代容器
短UUID，长UUID或者容器名称

</font>

参数：

-a 列出所有正在运行的容器和历史上运行过的容器

-l 显示最近创建的容器

-n 最近n个创建的容器

-q 静默模式，只显示容器的编号

ctrl+P+Q： 容器不停止退出 和 exit是 不相同
 

## 容器命名

```
sudo docker run --name myubuntu -i -t ubuntu  /bin/bash # 默认就是bin/bash
```

## 重新启动已经停止的容器
```
sudo docker start myubuntu

```

## docker  create 
创建一个容器，但是并不运行它，可以在自己的容器工作流中进行细粒度的控制

## docker attach 容器附着 
docker在重新启动的时候会沿用docker run 命令时的指定参数来运行 重新进入正在运行的容器并以命令行形式交互

因此容器在启动的时候会运行一个交互式的shell

```
sudo docker attach myubuntu 

docker exec -t myubuntu ls -l /tmp #在容器外运行并且将容器的运行结果返回给宿主机

-d 分离模式

-i 即使没有附加也保持stdin打开

-t 分配一个伪终端

exec 比attach的功能要更强大一点
```





## 创建守护式容器

<font color='yellow'>
$ sudo echo “hahah” >> test.csv
-bash: test.asc: Permission denied
这时可以看到 bash 拒绝这么做，说是权限不够。这是因为重定向符号 “>” 和 “>>” 也是 bash 的命令。我们使用 sudo 只是让 echo 命令具有了 root 权限，但是没有让 “>” 和 “>>” 命令也具有 root 权限，所以 bash 会认为这两个命令都没有像 test.csv文件写入信息的权限。

**解决方式**
第一种 第一种是利用 “sh -c” 命令，它可以让 bash 将一个字串作为完整的命令来执行，这样就可以将 sudo 的影响范围扩展到整条命令。具体用法如下：

$ sudo /bin/sh -c 'echo "hahah" >> test.asc'

另一种方法是利用管道和 tee 命令，该命令可以从标准输入中读入信息并将其写入标准输出或文件中，具体用法如下：

$ echo “hahah” | sudo tee -a test.asc

tee 命令的 “-a” 选项的作用等同于 “>>” 命令，如果去除该选项，那么 tee 命令的作用就等同于 “>” 命令

</font>

```
sudo docker run --name daemon_ubuntu -d ubuntu /bin/bash -c "while true; do echo hello world; sleep 1; done"


docker run -d centos # 这样之后利用docker ps 是不显示其已经运行的，这样做会导致docker前台没有应用，容器启动后会立即自杀，最佳解决方案是要运行的程序以前台进程的形式运行

```



## docker logs
查看容器内部的运行情况

```
sudo docker logs daemon_ubuntu
```
ctrl+c 退出内部日志跟踪



-t  加入时间戳

-f 跟随最新的日志打印 可以持续的追加

-tail 数字显示最后多少条

## docker支持日志驱动
可以将docker logs 日志功能禁用，将所有日志输出到syslog，但是目前我用不到这个功能

## docker top
查看容器内的进程
```
sudo docker top daemon_ubuntu
```

<font color='yellow'>
PID 和PPID 的含义表示什么还值得后续查找资料

经过查找:PID进程和PPID父进程的意思 
</font>

## docker stats
统计信息，显示一个或者多个容器的统计信息
```
sudo docker stats daemon_ubuntu
```
能够看到容器的CPU 内存 网络 I/O 存储 的性能和指标，能够快速地监控一个主机上的一组容器

这个是1.5 之后引入的命令

## 容器内部运行进程

在容器内运行交互式命令
```
sudo docker exec -t -i daemon_ubuntu  /bin/bash
```

## 停止守护式容器
```
sudo docker stop daemon_ubuntu # stop是温柔停止

sudo docker kill daemon_ubuntu # kill是强制停止，类似于拔电源
```
## 自动重启容器
--restart 标志会检查容器退出的代码，来判断是否要重启容器，默认行为是不重启

```
sudo docker run --restart=always --name daemon_ubuntu -d ubuntu /bin/bash -c "while true;echo hello world; sleep 1;done" 


```


<font color='yellow'>
除了always之外还有on-failure,当容器的退出代码为非0值时才会自动重启，可以指定失败的重启次数

--restart=on-failure:5
</font>

## docker inspect

```
sudo docker inspect daemon_ubuntu
```

返回配置信息，包括名称，命令，网络配置

-f 或者 --format可以选定查看结果
```
sudo docker inspect --format='{{.State.Running}}' daemon_ubuntu false
```
<font color='yellow'>
通过浏览/var/lib/docker 目录可以深入了解docker的工作原理，该目录下存放着docker镜像，容器和容器的配置，所有的容器都保存在/var/lib/docker/containers目录下
</font>

## 删除容器
```
sudo docker rm 容器id

sudo docker rm -f 删除运行中的docker容器，以前的版本需要先使用docker stop 或者docker kill命令来停止容器

```
**删除所有停用的容器**
```
sudo docker rm -f${docker ps -a -q}

sudo docker ps -a -q |xargs docker rm

# xargs 是linux 的可变参数

```


tips 删除所有容器
```
sudo docker rm 'sudo docker ps -a -q'
```

-a 表示列出所有容器，-q表示只需要返回容器的id而不返回其他的信息，相当于得到了所有容器的id列表

<font color="yellow">
${} 和 $()是有区别的
在bash中，$( )与` `（反引号）都是用来作命令替换的


$( )与｀｀
在操作上，这两者都是达到相应的效果，但是建议使用$( )，理由如下：

｀｀很容易与''搞混乱，尤其对初学者来说，而$( )比较直观。
最后，$( )的弊端是，并不是所有的类unix系统都支持这种方式，但反引号是肯定支持的

一般情况下，$var与${var}是没有区别的，但是用${ }会比较精确的界定变量名称的范围
</font>