---
title: GitBash的常用语句
date: 2019-09-03 06:54:51
tags: Git 心得
categories: 工具
---
# Git常用语句

## 初始化

git init 

在本地文件中打开Bash
输入git init

## 添加文件

git add .

## 添加特定的文件
git add 文件名

## 提交至主分支
git commit -m "双引号里面为注释的内容"

## 添加远程链接

git remote add origin https://github.com/jhshz520/algorithm_pratice

## 向远程更新代码前要先从远程获取最新版本代码
git pull origin master

## 最后是提交自己的代码
git push -u origin master

