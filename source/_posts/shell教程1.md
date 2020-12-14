---
title: shell教程1
date: 2020-11-17 11:27:53
categories: linux
tags: 工具
---

# shell脚本
一般情况下并不区分 
```
/bin/bash 和/bin/sh
```

## 第一个shell脚本
```
#!bash/bash
echo "hello world!"
```

### 运行方法
将上述代码保存为 **test_name.sh** ,cd到相应目录
```
chmod +x ./test.sh

./test.sh 

```

直接运行解释器

```
/bin/sh test.sh

/bin/php test/php

```

## shell变量

```
your_name="jhshz"

echo {(your_name}

echo $your_name# 使用变量，加美元符号能够使用，花括号是可选的，帮助解释器识别变量的边界


```

```
for skill in ada coffe Action java;do
    echo "I am good at ${skill}Script"# 避免把后面的Script连一起
done
```

### 只读变量 
```
readonly

#!/bin/bash
myurl="http://jhshz.top"
readonly myurl
myurl="http://www.google.com"

```

### 删除变量

```
unset
unset your_name# unset 不能删除只读变量
```

### 运行shell时的三种变量

1局部变量 局部变量在脚本或命令中定义，仅在当前shell实例中有效，其他shell启动的程序不能访问局部变量。

2环境变量 所有的程序，包括shell启动的程序，都能访问环境变量，有些程序需要环境变量来保证其正常运行。必要的时候shell脚本也可以定义环境变量。

3shell变量 shell变量是由shell程序设置的特殊变量。shell变量中有一部分是环境变量，有一部分是局部变量，这些变量保证了shell的正常运行
   
## shell字符串
单引号的限制：

        串内不能出现单独一个引号

双引号优点：
1 引号内可以有变量

2 可以出现转义字符
   

```
your_name="jhshz"
str="hello , I know you are \"$your_name" !\n"
echo -e $str

输出结果

hello, I know  you are "jhshz"!

```

## 拼接字符串

```
your_name="jhshz"
# 双引号拼接
greeting="hello,"$your_name"!"
greeting_1="hello,${your_name}!"

echo $greeting $greeting_1
# 单引号拼接
greeting_2='hello,$'your_name'!'
greeting_3='hello,${your_name}!'

echo $greeting_2 $greeting_3

```

输出结果：
```
hello, jhshz ! hello, jhshz !
hello, jhshz ! hello, ${your_name} !
```

### 获取字符串长度

```
str = "hello"
echo ${#str} # 输出5
```

### 提取子字符串

```
string="jhshz is excellent"

echo ${string:1:4}# 输出hshz
```

### 查找子字符串

```
string="runoob is excellent"

echo `expr index "$string" io`# 查找i或者o的位置，那个先找到就先输出那个，另外是反引号，不是单引号

```

### shell数组

```
arr_name=(value1,
value2,
value3
)# 定义

arr_name[0]=value0
arr_name[1]=value1# 单独定义分量

${数组名[下标]}

valuen=${arr_name[n]}

echo ${arr_name[@]}# @能获取数组中的所有元素

```

### 获取数组的长度
```
length=${#arr_name[@]}

or

length=${#arr_name[*]}#获取单个数组的长度

length=${#arr_name[n]}
```

### 多行注释

```
:<<EOF
注释内容
EOF




:<<!
注释内容
!
```
