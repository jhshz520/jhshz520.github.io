---
title: shell教程5
date: 2020-11-17 15:26:43
categories: linux
tags: 工具
---
# shell函数
定义格式
```
[ function ] funname [()]

{

    action;

    [return int;]

}

```
实例
```
#!/bin/bash
demofun(){
    echo "this is a founction demo"
}

echo "founction start"
demofun
echo "founction end"
```

## 带有return语句的返回
```
funwithreturn(){
    echo "input num1"

    read num1

    echo "input num2"

    read num2

    return $(($num1+$num2))
}

funwithreturn
echo "the sum of two number $? !"

# 函数的返回值可以通过 $? 来获得
```

## 带有参数的函数
```
funWithParam(){
    echo "第一个参数为 $1 !"
    echo "第二个参数为 $2 !"
    echo "第十个参数为 $10 !"
    echo "第十个参数为 ${10} !"
    echo "第十一个参数为 ${11} !"
    echo "参数总数有 $# 个!"
    echo "作为一个字符串输出所有参数 $* !"
}
funWithParam 1 2 3 4 5 6 7 8 9 34 73# 后面跟的就是调用参数的列表
```
<font color="yellow">
1. $? 仅对其上一条指令负责，一旦函数返回后其返回值没有立即保存入参数，那么其返回值将不再能通过 $? 获得
2. 函数与命令的执行结果可以作为条件语句使用，注意的是，和C语言不同，shell语言中0表示true，0以外的值代表false
</font>

