---
title: 解决Gitbash中文乱码问题
date: 2019-11-09 16:52:16
categories: 工具
tags: Git
---
# GitBash 
不知道何时GItbash出现了中文乱码问题，而且在我安装Tomcat之后，运行startup 命令也会出现很多的乱码

这是我不能容忍的

# 解决方法
 
 在git bash框中任意位置右击
 
 点击设置菜单中的option
 
 选择Text页
 Locale项选zh_CN，
 Character set项选utf-8
如此可以解决命令行返回注解中文乱码

# tomcat 的startup命令的乱码问题并没有解决

第一步：找到Tomcat解压后名为conf的文件夹，双击打开找到logging.properties文件

第二步：打开logging.properties文件，将里面的
java.util.logging.ConsoleHandler.encoding = UTF-8
改为
java.util.logging.ConsoleHandler.encoding = GBK
# 解决LF will be replaced by CRLF in

## 问题分析
换行符在window下是CRLF， linux是LF，在执行git add 时会提示自动转换。（工作区的文件都应该用 CRLF 来换行，但是我们的项目文件里使用了LF作为换行符，所以弹出提示）

## 解决方法
1 禁用自动转换
```
git config --global core.autocrlf false
```
2 忽略 