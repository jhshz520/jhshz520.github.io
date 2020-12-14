---
title: social-oauth-python
date: 2020-09-19 21:36:50
categories: 项目开发
tags: python
---
# Github登录
需要输密码时的登录界面
```
https://github.com/login?client_id=776ca8fdb3919f16e2f8&return_to=%2Flogin%2Foauth%2Fauthorize%3Fclient_id%3D776ca8fdb3919f16e2f8%26redirect_uri%3Dhttp%253A%252F%252Fwww.hustshub.cn%252Fcomplete%252Fgithub%252F%26response_type%3Dcode%26scope%3Drepo%252Cuser%26state%3DDgKizvSEIZN00YZNGUzmZAMXJWBNk2S4



https://github.com/login/oauth/authorize?client_id=776ca8fdb3919f16e2f8&redirect_uri=http://www.hustshub.cn/complete/github/&state=DgKizvSEIZN00YZNGUzmZAMXJWBNk2S4&response_type=code&scope=repo,user
```
```
第一个问号之前到authorize为止代表的是{%url 'social:begin' 'github'%}
next = 之后的 


把鼠标放在github的登录按钮上的时候，上面的链接地址指向的并非是上面的url，而是http://www.hustshub.cn/login/login/?next=http://hustshub.cn/login/为什么登录github认证就变了
```
登录时的授权连接：

```
https://github.com/login/oauth/authorize?scope=repo,user&redirect_uri=http://hustshub.cn/complete/github/&client_id=776ca8fdb3919f16e2f8&state=CFbvKE5eMMpsSlYd40M6MW9bnxrU9PMP&response_type=code
```

# 未经更改的grid链接跳转
```
http://hustshub.cn/login/gridoauth2/?next=http://hustshub.cn/login/

```
# html的更改
class在static里面，img选项里面有各种图片的设置，我现在借用的是google的登录设置

# 回调的地址应该怎么使用？
很多网站回调的地址设置都是
```
/complete/callback
"{%url 'social:begin' 'github'%}?next={{domain}}{{request.path}}"

这是django框架特有的反向解析，还有带参数的反向解析
```

# pycharm 进行断点调试
利用debug模式
# 怎么启动django项目
想用pycharm 启动django项目
提示没有django模块
默认的python解释器是在 /usr/bin/python3.5目录下

在settings 里面的虚拟环境下选择已存在的python解释器环境

依然会存在没有django的问题，可能是conda环境没有装django的相关依赖，因此又利用conda install django
重新安装了一遍django

# conda中的python从3.6更新到3.7之后，会出现conda找不到模块的问题
Anaconda3-5.3.1-Linux-x86_64.sh
重新安装