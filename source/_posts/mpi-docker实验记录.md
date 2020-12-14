---
title: mpi_docker实验记录
date: 2020-11-23 20:09:29
categories: 实验
tags: 实验
---
# NLKNguyen/alpine-mpich实验

## single host
获取id_rsa.pub：

ssh-keygen

一路回车会看到一个矩形图案

这时ls看不到 .ssh文件夹，需要 cd  /root/ssh/

这样会切换到.ssh 再ls 能够看到id_rsa 和 id_ras.pub


ssh: Could not resolve hostname keygen: Temporary failure in name resolution

这是因为打错了应该是ssh-keygen，生成的id_rsa和id_rsa.pub 在 /home/Hadoop/.ssh文件夹下 将其复制到 alpine-mpich cluster ssh文件夹下进行替换
### 问题
```
./cluster.sh login

WARNING: Error loading config file: /home/hadoop/.docker/config.json: stat /home/hadoop/.docker/config.json: permission denied
```

虽然报错但是依然能运行出正确的结果

## 试试能不能跑benchmark

### npb 测试集怎么使用呢
sudo curl -L "https://github.com/docker/compose/releases/download/1.27.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose


## 所有虚拟机的ip
ubuntu1 192.168.110.129
ubuntu_slave1 192.168.110.8 
ubuntu_slave2 192.168.110.9
mpi_single 192.168.110.12 ifconfig 显示是 192.168.110.11 还没有改正

linux 设置静态ip

vi /etc/network/interface

dns-nameserver 202.114.0.131


## mpirun 错误
mpirun -np 8 ./is.A.8
[proxy:0:2@7580adeb3a13] HYDU_create_process (utils/launch/launch.c:75): execvp error on file ./is.A.8 (No such file or directory)

## 为什么在master节点的命令行工具中不能进行 先编译后运行的操作

具体是因为核数不对还是因为在一开始并没有通过 registry容器进行容器镜像的分发

事实证明是因为没有分发的原因，如果把dockerfile的文件进行相关的改动就可以进行测试了


mpirun hello_world实验实验