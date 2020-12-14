---
title: 常用Dos命令
date: 2019-10-21 14:47:13
tags: 命令行工具
---
# Windows 下切换盘符
直接输入盘符的名称加冒号 例如 D:

# cd
change directory
# dir
directory ，列出目录中的文件名
/ p 分屏显示

h 显示隐藏文件
s 显示系统文件
d 显示目录
# md mkdir

make directory

md back，创建一个名为back的文件夹

# rd 
remove dirctory

rd 只能够删除空目录

rd 后面跟的是路径或名称

rd 不能够直接删除文件

# path

设置命令搜索向导

# ren 
rename
rename oldname newname
# type
显示文件的内容

不能直接显示文件夹的名字
# copy
copy sourcefile newfile

可以指定专门的后缀名称的文件例

copy *.com c\:dos

copy 命令合并多个文件
例 copy 1.txt+2.txt 12.txt

# fc 命令 

文件比较，file compare 

/c 忽略大小写字母的差异

# del 

删除文件而非文件夹

/p 输出之前等待确认


# --------------------------------------

# 网络命令

## telnet 命令

telnet \\RemoteServe

指定要连接的服务器的名称

如果不指定名称，则会默认启用客户端

quit 命令可以退出 telnet 提示符的模式

# open \\RemoteServe port

指定服务器名和端口号

# close 
关闭与远程计算机的连接

# set
 设置

# display 

查看当前的设置

# send 
发送telnet client 命令

# arp 

显示和修改本地arp列表
-a
显示本地arp表

# at

计划管理程序

# ping

-a 对目的地ip地址进行反向名称解析，如果解析成功将返

回相应的主机名

# ipconfig

/all 显示所有适配器的完整tcp/ip配置信息

# netstat

显示当前网络状态

-a 显示所有活动的tcp和udp

-e 以太网统计信息

-r 路由表内容

-p prococol 显示所制定的协议

# 配置防火墙开启80端口
 sudo apt-get install ufw

sudo ufw enable

sudo ufw allow 80

sudo ufw delete allow 80

80端口只开放tcp协议

sudo ufw allow 80/tcp

sudo ufw delete allow 80/tcp


