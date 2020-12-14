---
title: shub项目部署
date: 2020-09-21 14:41:20
categories: 项目开发
tags: Django
---
# 安装开发环境
## linux更换apt-get源
```
sudo cp /etc/apt/sources.list /etc/apt/sources.list_backup

sudo gedit /etc/apt/souces.list
```
在网上搜索清华源或者阿里源进行加入,并且刷新列表
```
sudo apt-get update

```

## ubuntu 下载安装docker 和docker-compose
docker安装
```
$ sudo apt-get update

$ sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common



curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

sudo apt-key fingerprint 0EBFCD88


$ sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"


 $ sudo apt-get update
 $ sudo apt-get install docker-ce docker-ce-cli containerd.io


```
测试Docker引擎是否安装成功
```
$ sudo docker run hello-world
```

docker-compose 安装
```
sudo curl -L "https://github.com/docker/compose/releases/download/1.27.3/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

sudo chmod +x /usr/local/bin/docker-compose

sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose


```
查看是否安装成功
```
$ docker-compose --version
```

## github拉取镜像


## docker-compose 进行编译，编排启动

## 重装了一次系统，ip地址变了，需要在阿里云重新绑定域名
```
202.114.6.205
```

## ubuntu换源注意事项 
有时候ubuntu 换源之后再去更改其他的会出现自动更新的问题这可能是清华源的问题，如果不经改动可能会有预览版本的包进行了安装导致不能进入桌面的操作环境
这个时候由于缺少相应的显卡驱动，对于新手来说，这种事情是不好处理的，而且校园网还需要认证页面进行认证不能直接连接网络，这就造成了比较大的困扰智能重装系统来搞定
unanme -a 4.15.0-112 记住这个版本便于后续的调查和回退研究

## 安装Anaconda3

## 安装pycharm

## 下载文件

## 怎么配置django进行本地的调试
### 建立一个虚拟环境
在虚拟环境中安装django所需要的python依赖库
```
pip install -r requirements.txt
```
将依赖进行安装，设置pycharm的编译器为虚拟环境中的shub
## 怎么查80端口被占用

方法1

```
ps -ef |grep 80 

```

UID     ：程序被该 UID 所拥有

PID     ：就是这个程序的 ID 

PPID    ：则是其上级父程序的ID

C       ：CPU使用的资源百分比

STIME   ：系统启动时间

TTY     ：登入者的终端机位置

TIME    ：使用掉的CPU时间。

CMD     ：所下达的是什么指令



<font color='yellow'>

最常用的有ps -ef 和ps aux

ps -ef 是用标准的格式显示进程的

UID    //用户ID、但输出的是用户名 


PID    //进程的ID 

PPID    //父进程ID 

C      //进程占用CPU的百分比 

STIME  //进程启动到现在的时间 

TTY    //该进程在那个终端上运行，若与终端无关，则显示? 若为pts/0等，则表示由网络连接主机进程。 

CMD    //命令的名称和参数

ps aux 是用BSD的格式来显示

同ps -ef 不同的有列有
USER      //用户名 

%CPU      //进程占用的CPU百分比

%MEM      //占用内存的百分比 

VSZ      //该进程使用的虚拟內存量（KB）

RSS      //该进程占用的固定內存量（KB）（驻留中页的数量） 

STAT      //进程的状态 

START    //该进程被触发启动时间 

TIME      //该进程实际使用CPU运行的时间

其中STAT状态位常见的状态字符有
D      //无法中断的休眠状态（通常 IO 的进程）；

R      //正在运行可中在队列中可过行的；

S      //处于休眠状态； 

T      //停止或被追踪； 

W      //进入内存交换 （从内核2.6开始无效）； 

X      //死掉的进程 （基本很少见）； 

Z      //僵尸进程； 

<      //优先级高的进程 

N      //优先级较低的进程 

L      //有些页被锁进内存； 

s      //进程的领导者（在它之下有子进程）；

l      //多线程，克隆线程（使用 CLONE_THREAD, 类似 NPTL pthreads）； 

/ /+     //位于后台的进程组；
    
</font>

```

方法2

netstat -anp |grep :80  

方法3

lsof -i:80 

方法 4

netstat -tunlp |grep :80 

方法5

netstat -an |grep :80

```

