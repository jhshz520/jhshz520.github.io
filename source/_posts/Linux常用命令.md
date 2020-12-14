---
title: Linux常用命令
date: 2019-11-29 11:38:47
categories: linux
tags: 工具
---
# linux常用命令
## linux终端快捷键

```
ctrl+alt+n  新建一个终端

ctrl+shift+n 已有一个终端的情况下,新开一个终端

ctrl+shift+t 已有一个终端的情况下，新开一个终端，并且连个终端是并行的打开在一个窗口
```
## 常用命令
## 查看系统内核和架构
uname -a
## 命令行访问网页
DOS下的网页都是纯文本的，而且都不支持javascript、flash，甚至不支持CSS
## mysql -u root -p 和直接在控制台下运行的mysql是两个不同的数据库

## ERROR 1290 (HY000): The MySQL server is running with the --secure-file-priv option

## su root
输入密码 切换至root用户

## 如何删除root用户才能拥有权限的文件
rm -r 文件夹名
## 网络校对时间
ntpdate 时间同步命令
apt-get install ntpdate
sudo nptdate 210.72.145.44(国家授时中心)
ntpdate -u ntp.api.bz :网络时间同步命令

    注意：若不加上-u参数， 会出现以下提示：no server suitable for synchronization found
有时候时间并不准确还需要手动设置
 sudo date -s 17:20:30
## 解压文件到指定文件目录
sudo tar -C /home/jhshz/Program/MyGo/ -xzvf go1.13.4.linux-amd64.tar.gz
而且这部操作必须是进入到待解压文件所在目录下进行操作
## chmod +x的意思是赋予执行权限
chmod a +X  文件或者目录
给与所有用户增加执行权限
## SSH
ssh 远程控制工具默认端口号是22，linux
服务器可以配置成ssh服务器客户可以通过ssh进行远程登录
## 查看本地ip
ip addr show

ifconfig

# 安装软件
sudo dpkg -i 所要安装的软件名

# uname -a
显示内核和架构

# 80端口占用
netstat -lnp|grep 80

# debian 安装的是iptables
 1.  查看已打开的端口  # netstat -anp

  iptables -A INPUT -ptcp --dport  8099 -j ACCEPT
 2. 查看想开的端口是否已开 # firewall-cmd --query-port=666/tcp
  若此提示 FirewallD is not running 
  表示为不可知的防火墙 需要查看状态并开启防火墙

 3. 查看防火墙状态  # systemctl status firewalld
 running 状态即防火墙已经开启
 dead 状态即防火墙未开启
 4. 开启防火墙，# systemctl start firewalld  没有任何提示即开启成功
 5. 开启防火墙 # service firewalld start  
   关闭防火墙 # systemctl stop firewalld
   centos7.3 上述方式可能无法开启，可以先#systemctl unmask firewalld.service 然后 # systemctl start firewalld.service

 6. 查看想开的端口是否已开 # firewall-cmd --query-port=666/tcp    提示no表示未开
 7. 开永久端口号 firewall-cmd --add-port=666/tcp --permanent   提示    success 表示成功
 8. 重新载入配置  # firewall-cmd --reload    比如添加规则之后，需要执行此命令
 9. 再次查看想开的端口是否已开  # firewall-cmd --query-port=666/tcp  提示yes表示成功
 10. 若移除端口 # firewall-cmd --permanent --remove-port=666/tcp

 11. 修改iptables  有些版本需要安装iptables-services # yum install iptables-services 然后修改进目录 /etc/sysconfig/iptables   修改内容

# ls 
ls 命令 -l 长模式输出，-l的用法非常常用能够输出很多的有用信息

-t 按照修改时间的先后顺序进行排序

-S 输出结果按照文件的大小来进行排序

ls ~ 代表访问家目录
会列出家目录下所有文件目录




# file filename
打印文件内容的简单描述

## less
less 浏览文本文件的内容

less运行起来后可以查看文件的内容，page up向上翻滚一页

page down 或者space 向下翻动一页

q 退出less程序

# cp
复制文件和目录
```
-a  --archive 复制文件的目录，包括他们的属性，权限等
-i --interactive 重写已存在的文件之前，提示用户进行确认
-r  --recursive' 递归复制
-u 仅复制目录中不存在的或者较新的文件
-v --verbose 显示详实的命令操作信息   
```
# 通配符及其意义
/*  匹配任意字符（包括零个或者一个）
/？ 匹配任意一个字符（不包括0个）
/[/] 匹配任意一个属于字符集中的字符

/[/!/]  匹配任意一个不是字符集中的字符
/[/:class:/] 匹配任意一个属于指定字符类中的字符

## 最常用的通配符
```
[:alnum:] 匹配任意一个字母或者数字
[:digit:]  数字
[:alpha:]  字母
[:lower:]  小写字母
[:upper:]  大写字母
```

## 下面主要是最常使用的字符类
```
/* 所有文件

/g* 以g为开头的文件

b*.txt  以b为开头，中间任意多个字符，以txt结尾的文件

Data？？？  以Data 开头，紧接着三个字符的文件

[abc]*   文件名以a或者b或者c开头的文件

z[0-9][0-9][0-9] 以z开头跟着3个数字的文件
[[:upper:]]* 以大写字母为开头的wenjian
[![:digit:]]* 不以数字开头的文件
 

```
## rm
```
-r 递归删除
-f 强制删除
rm -i 不加i就会默默删除
 
```
# ln

创建链接
## 硬链接
ln fun dir1/funhard

## 软连接

ln -s fun dir1/fun-sym


## linux关闭一个窗口
ctrl+w 关闭当前窗口

alt+F4

关闭所有应用程序
### 新建窗口

ctrl+alt+T

## grep
 
强大的正则表达工具

## cat
cat -n 也能够打印行号




## awk 

利用cat去打印某一列
```
cat name.txt | awk '{print $4}'

cat name.txt | awk '{print $NF}' # 打印最后一列，注意NF是大写
```
## set命令
如果命令行下不带任何参数，直接运行set，会显示所有的环境变量和 Shell 函数
### set -u
执行脚本的时候，如果遇到不存在的变量，Bash 默认忽略它,set -u

遇到不存在的变量就会报错，并且立即停止执行

例子
```
#!/usr/bin/env bash

echo $a
echo bar
```
echo $a输出了一个空行，Bash 忽略了不存在的$a，然后继续执行echo bar。大多数情况下，这不是开发者想要的行为，遇到变量不存在，脚本应该报错，而不是一声不响地往下执行。

```
#!/usr/bin/env bash
set -u

echo $a
echo bar
```
-u还有另一种写法-o nounset，两者是等价的

```
set -o nounset
```


### set -x
默认情况下，脚本执行后，屏幕只显示运行结果，没有其他内容。如果多个命令连续执行，它们的运行结果就会连续输出。有时会分不清，某一段内容是什么命令产生的。

set -x用来在运行结果之前，先输出执行的那一行命令
```
#!/usr/bin/env bash
set -x

echo bar

```
还有另一种写法-o xtrace

```
set -o xtrace
```
### 错误处理

如果脚本里面有运行失败的命令（返回值非0），Bash 默认会继续执行后面的命令

```
#!/usr/bin/env bash

foo
echo bar
```
上面脚本中，foo是一个不存在的命令，执行时会报错。但是，Bash 会忽略这个错误，继续往下执行
可以看到，Bash 只是显示有错误，并没有终止执行。

这种行为很不利于脚本安全和除错。实际开发中，如果某个命令失败，往往需要脚本停止执行，防止错误累积。这时，一般采用下面的写法。

```
command || exit 1
```
上面的写法表示只要command有非零返回值，脚本就会停止执行。

如果停止执行之前需要完成多个操作，就要采用下面三种写法

```
# 写法一
command || { echo "command failed"; exit 1; }

# 写法二
if ! command; then echo "command failed"; exit 1; fi

# 写法三
command
if [ "$?" -ne 0 ]; then echo "command failed"; exit 1; fi
```

### set -e

```
#!/usr/bin/env bash
set -e

foo
echo bar
```

set -e从根本上解决了这个问题，它使得脚本只要发生错误，就终止执行。


但是，某些命令的非零返回值可能不表示失败，或者开发者希望在命令失败的情况下，脚本继续执行下去。这时可以暂时关闭set -e，该命令执行结束后，再重新打开set -e

```
set +e
command1
command2
set -e
```
set +e 表示关闭-e选项， -e表示重新打开

<font color='yellow'>
set -e有一个例外情况，就是不适用于管道命令。

所谓管道命令，就是多个子命令通过管道运算符（|）组合成为一个大的命令。Bash 会把最后一个子命令的返回值，作为整个命令的返回值。也就是说，只要最后一个子命令不失败，管道命令总是会执行成功，因此它后面命令依然会执行，set -e就失效了。

</font>

例子
```
#!/usr/bin/env bash
set -e

foo | echo a
echo bar
```

set -o pipefail用来解决这种情况，只要一个子命令失败，整个管道命令就失败，脚本就会终止执行

```
#!/usr/bin/env bash
set -eo pipefail

foo | echo a
echo bar
```

## tree命令