---
title: shell教程3
date: 2020-11-17 09:08:54
categories: linux
tags: 工具
---
# shell命令

## echo 命令
显示变量
```
read name # 从标准输入中读入一个name 变量
echo "$name It is a test"
```
显示换行
```
echo -e "ok!\n" # -e 开启转义，不知道为什么我的-e依然会在命令行中显示出来
echo "it is a test"

echo -e "ok!\c" # -e 开启转义 \c不换行
echo "it is a test"

```
### 显示结果重定向到文件
```
echo "it is a test echo to file " > myfile #会生成myfile，隐藏文件 
```
### 原样输出字符串，不进行转义和取变量

```
echo '$name\"'
```
### 显示命令的执行结果
```
echo `date`
```

## printf命令
printf 命令模仿 C 程序库（library）里的 printf() 程序printf 由 POSIX 标准所定义，因此使用 printf 的脚本比使用 echo 移植性好。
外面可以在 printf 中使用格式化字符串，还可以制定字符串的宽度、左右对齐方式等。默认 printf 不会像 echo 自动添加换行符，我们可以手动添加 \n。
```
printf "%-10s %-8s %-4s\n" 姓名 性别 体重kg  
printf "%-10s %-8s %-4.2f\n" 郭靖 男 66.1234 
printf "%-10s %-8s %-4.2f\n" 杨过 男 48.6543 
printf "%-10s %-8s %-4.2f\n" 郭芙 女 47.9876 
```
## test命令
test 命令用于检查某个条件是否成立，它可以进行数值、字符和文件三个方面的测试

### 数值测试
```
-eq	等于则为真
-ne	不等于则为真
-gt	大于则为真
-ge	大于等于则为真
-lt	小于则为真
-le	小于等于则为真
```
实例
```
#!/bin/bash

num1=100
num2=100
if test $[num1] -eq $[num2]
then
    echo '两个数相等！'
else
    echo '两个数不相等！'
fi

a=5
b=6

result=$[a+b] # 注意等号两边不能有空格
echo "result 为： $result"
```

### 字符测试
```
=	等于则为真
!=	不相等则为真
-z 字符串	字符串的长度为零则为真
-n 字符串	字符串的长度不为零则为真
```
实例
```
num1="ru1noob"
num2="runoob"
if test $num1 = $num2
then
    echo '两个字符串相等!'
else
    echo '两个字符串不相等!'
fi
```
### 文件测试
```
-e 文件名	如果文件存在则为真
-r 文件名	如果文件存在且可读则为真
-w 文件名	如果文件存在且可写则为真
-x 文件名	如果文件存在且可执行则为真
-s 文件名	如果文件存在且至少有一个字符则为真
-d 文件名	如果文件存在且为目录则为真
-f 文件名	如果文件存在且为普通文件则为真
-c 文件名	如果文件存在且为字符型特殊文件则为真
-b 文件名	如果文件存在且为块特殊文件则为真
```
实例
```
cd /bin
if test -e ./notFile -o -e ./bash
then
    echo '至少有一个文件存在!'
else
    echo '两个文件都不存在'
fi
```