---
title: shell教程6
date: 2020-11-17 15:27:11
categories: linux
tags: 工具
---
# shell命令 输入与输出的重定向
一个命令通常从一个叫标准输入的地方读取输入，默认情况下，这恰好是你的终端。同样，一个命令通常将其输出写入到标准输出，默认情况下，这也是你的终端
## 重定向命令列表
command > file	将输出重定向到 file。

command < file	将输入重定向到 file。

command >> file	将输出以追加的方式重定向到 file。

n > file	将文件描述符为 n 的文件重定向到 file。

n >> file	将文件描述符为 n 的文件以追加的方式重定向到 file。

n >& m	将输出文件 m 和 n 合并。

n <& m	将输入文件 m 和 n 合并。

<< tag	将开始标记 tag 和结束标记 tag 之间的内容作为输入。

## 输出重定向

```
who > myfile
```
重定向输出会覆盖文件的内容，如果不希望文件的内容被覆盖，可以使用>> 追加到文件结尾

例子
```
echo "jhshz520" >> myfile
```

## 输入重定向

```
wc -l myfile # 统计文件的行数，结果会输出文件名

wc -l < myfile
```

## 重定向的深入讲解
一般情况下，每个 Unix/Linux 命令运行时都会打开三个文件：

标准输入文件(stdin)：stdin的文件描述符为0，Unix程序默认从stdin读取数据。

标准输出文件(stdout)：stdout 的文件描述符为1，Unix程序默认向stdout输出数据。

标准错误文件(stderr)：stderr的文件描述符为2，Unix程序会向stderr流中写入错误信息。

默认情况下，command > file 将 stdout 重定向到 file，command < file 将stdin 重定向到 file
```
commond 2 > file #stderr 重定向到file

command 2 >> file # stderr 重定向追加到file

command > file 2&1 # stdout和stderr合并后重定向到file

command >> file 2&1 # stdout和stderr合并后追加重定向到file

$ command < file1 >file2

command 命令将 stdin 重定向到 file1，将 stdout 重定向到 file2。

```

## /dev/null文件

如果希望执行某个命令，但又不希望在屏幕上显示输出结果，那么可以将输出重定向到 /dev/null
```
$ command > /dev/null 2>&1
```

## shell 文件包含

和其他语言一样，Shell 也可以包含外部脚本
```
. filename   # 注意点号(.)和文件名中间有一空格

或

source filename
```
test1.sh
```
#!/bin/bash
# author:菜鸟教程
# url:www.runoob.com

url="http://www.runoob.com"

```

test2.sh
```
#!/bin/bash
# author:菜鸟教程
# url:www.runoob.com

#使用 . 号来引用test1.sh 文件
. ./test1.sh

# 或者使用以下包含文件代码
# source ./test1.sh

echo "菜鸟教程官网地址：$url"
```
