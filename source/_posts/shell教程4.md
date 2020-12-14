---
title: shell教程4
date: 2020-11-17 09:09:17
categories: linux
tags: 工具
---
# shell流程控制
sh的流程控制不可为空
就是说如果没有else分支语句就省略不写，但是不能为空
```
if condition
then
    command1 
    command2
    ...
    commandN 
fi
```
写成一行则适用于终端命令行
```
if [$(ps -ef |grep -c "ssh") -gt 1];then echo "true";fi
```
## if else 格式
```
if condition
then
    command1 
    command2
    ...
    commandN
else
    command
fi
```
## if else if 格式
```
if condition1
then
    command1
elif condition2 
then 
    command2
else
    commandN
fi
```
判断a和b的大小
```
a=10
b=20
if [ $a == $b ]
then
   echo "a 等于 b"
elif [ $a -gt $b ]
then
   echo "a 大于 b"
elif [ $a -lt $b ]
then
   echo "a 小于 b"
else
   echo "没有符合的条件"
fi

```

if else 语句经常和test命令结合适用
```
num1=$[2*3]
num2=$[1+5]
if test $[num1] -eq $[num2]
then
    echo '两个数字相等!'
else
    echo '两个数字不相等!'
fi
```
## for循环
格式
```
for var in item1 item2 ... itemN
do
    command1
    command2
    ...
    commandN
done
```
写成一行
```
for var in item1 item2 item3 item4; do command1;command2;done;
```
例子
顺序输出当前列表的数字
```
for loop in 1 2 3 4 5
do
    echo "the value is: $loop"
done
```
顺序输出字符串中的字符
```
for str in "this is a string"
do
    echo $str
done
```

## while循环
```
#!/bin/bash
int=1
while(( $int<=5 ))
do
    echo $int
    let "int++"# ubuntu let 报错是因为ubuntu的bash用的是dash，是阉割版的bash，可以用以下方法代替
    sudo dpkg -reconfigure dash
done
```
## 无限循环
```
while true
do
    command
done

或者

for (( ; ; ))


```

## until循环

```
a=0

until [ ! $a -lt 10 ]
do
   echo $a
   a=`expr $a + 1`
done
```
<font color="yellow">
一定要注意方括号和变量之间一定要留有空格，否则会报错
</font>

## 多选择语句case
```
case 值 in
模式1)
    command1
    command2
    ...
    commandN
    ;;
模式2）
    command1
    command2
    ...
    commandN
    ;;
esac
```



```
echo '输入 1 到 4 之间的数字:'
echo '你输入的数字为:'
read aNum
case $aNum in
    1)  echo '你选择了 1'
    ;;
    2)  echo '你选择了 2'
    ;;
    3)  echo '你选择了 3'
    ;;
    4)  echo '你选择了 4'
    ;;
    *)  echo '你没有输入 1 到 4 之间的数字'
    break
    ;;
esac
```

```
#!/bin/sh

site="runoob"

case "$site" in
   "runoob") echo "菜鸟教程"
   ;;
   "google") echo "Google 搜索"
   ;;
   "taobao") echo "淘宝网"
   ;;
esac
```