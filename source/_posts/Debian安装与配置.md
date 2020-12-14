---
title: Debian安装与配置
date: 2019-10-25 13:26:16
tags: Docker环境
categories: 工具
---
# 安装了Debian10 双系统
# 输入法的配置
# sudo权限的更改
# 安装了vim
# 配置了Debien的国内源
# 解决debian10的wifi问题

1 apt-get install iwilwifi

2 modprobe -r iwlwifi; modprobe iwlwifi

3 lspci 查看网卡的型号搜索相关的驱动

4 apt-get install firmware-realtek

5 最后重启电脑，能够设置成功
