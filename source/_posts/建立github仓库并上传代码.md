---
title: 建立github仓库并上传代码
date: 2020-09-16 18:51:14
categories: 工具
tags: Git
---
# 登录github并新建一个仓库，这里我们新建一个私人仓库

# 上传项目到github
## 需要安装git
## 将远程的仓库copy到本机中
```
git clone https://github.com/xian123/azure-linux-automation.git
```
## 将想要提交的文件夹copy到该目录下
执行
```
git add .
```
表示将该目录下所有改动的文件夹和文件都添加到版本管理器中


执行
```
git commit -m "First commit"
```
-m 参数表示可以直接输入后面的“message”，如果不加 -m参数，那么是不能直接输入message的，而是会调用一个编辑器一般是vim来让你输入这个message

表示提交到本地的版本控制库里面，引号里面的内容就是本次提交的说明信息


提交报错

```
please tell me who you are 


解决方案：
git config --global user.email "you@example.com"

git config --global user.name "your name"

```


执行
```
git push origin master
```
将本地仓库提交到远程的github中，需要输入用户名和密码，密码输入的时候没有任何的提示信息

# 刷新github页面就能看到上传的代码了



# github分支创建
## git branch hustshub
创建分支 hustshub

创建分支 gridshub_ipc
```
git branch gridshub_ipc
```
## git checkout hustshub
切换分支到hustshub

切换分支到gridshub_ipc

```
git checkout gridshub_ipc
```

<font color="yellow">

当一个很久没有的项目，切换分支时报错

error: pathspec 'master' did not match any file(s) known to git
</font>

**问题解决**
```
git branch -a
查看分支状况
```
没有想要切换的分支，需要先获取分支
```
git fetch
```
把某个分支上的内容都拉取到本地

```
git pull origin dev(远程分支名称)
```

## git push origin hustshub

将分支上传
<font color="yellow">

这里只是将分支进行上传，并没有进行相关的代码更改
</font>
## 可以直接在github上编辑也可以在本地编辑完之后上传到不同的分支


## 只克隆分支下的代码
例如我的项目是gridshub，下面有两个分支一个是master分支，另外一个是hustshub分支
```
git clone -b hustshub https://github.com.jhshz520/gridshub.git


```
与
```
git clone https:/github.com.jhshz520/gridshub.git


```
进行对比




## 
我的三个版本的仓库

hustshub: 现在部署在香港的服务器上

<font color='red'>
今天发现一个重大的问题，就是香港服务器的 gridshub/shub/settings中的 auth.py

**登录成功之后的回调路由进行了改正**
SOCIAL_AUTH_LOGIN_REDIRECT_URL = "http://hustshub.cn"#登录成功之后的回调路由

**但是在 config.py 中还没有修改**

DOMAIN_NAME = "http://gridshub.cn"
DOMAIN_NAME_HTTP = "http://gridshub.cn"
DOMAIN_NAKED = DOMAIN_NAME_HTTP.replace("http://", "")


**可能这就是**

中科院老师说的能够正常进入，但是页面退出以后却不能正常退出的原因，

</font >

需要去验证一下上面的DOMAIN_NAME 具体是在哪使用的，通过这个

来判断是否是执行退出操作时没有进行相关的退出

**pycharm利用ctrl+shift+f**
全局搜索某个字符串


gridshub:




gridshub_ipc:现在部署在阿里云北京节点，因为需要进行相关的备案信息


## 利用git将本地的代码和远程同步

需要先切换分支

```
git checkout gridshub_ipc

如果上面的不能成立那么先

git fetch


最后 

git pull origin gridshub_ipc
```


**git pull 卡在 Unpacking objects:  68% (39/57)**


可能是因为项目太大了，所以删除了已经上传的镜像文件，另外也有可能是https的缘故

可以增大https.buffer

```
git config --global http.postBuffer 524288000

git config --global http.postBuffer 524288000

# windows下也有可能存在卡住问题，经过测试并非是代理的问题
git config --global sendpack.sideband false


```

## git 冲突


Please, commit your changes or stash them before you can merge.


1、保留本地的修改 的改法
1）直接commit本地的修改 ----也一般不用这种方法

2）通过git stash  ---- 通常用这种方法


git stash
git pull
git stash pop
通过git stash将工作区恢复到上次提交的内容，同时备份本地所做的修改，之后就可以正常git pull了，git pull完成后，执行git stash pop将之前本地做的修改应用到当前工作区。

git stash: 备份当前的工作区的内容，从最近的一次提交中读取相关内容，让工作区保证和上次提交的内容一致。同时，将当前的工作区内容保存到Git栈中。

git stash pop: 从Git栈中读取最近一次保存的内容，恢复工作区的相关内容。由于可能存在多个Stash的内容，所以用栈来管理，pop会从最近的一个stash中读取内容并恢复。

git stash list: 显示Git栈内的所有备份，可以利用这个列表来决定从那个地方恢复。

git stash clear: 清空Git栈。此时使用gitg等图形化工具会发现，原来stash的哪些节点都消失了。

2、放弃本地修改 的改法  ----这种方法会丢弃本地修改的代码，而且不可找回

```
git reset --hard
git pull<br><br><br><br><br><br>

git reset HEAD

有时候会存在忘记拉取更新就开始进行本地开发的工作

强制覆盖本地代码
git fetch --all

```
## 一分钟解决git clone速度极慢的问题
使用国内代理
```
在github.com之后加上cnpmjs.org # 实测有效


//这是我们要clone的
git clone https://github.com/Hackergeek/architecture-samples
 
//使用镜像
git clone https://github.com.cnpmjs.org/Hackergeek/architecture-samples
 
//或者
 
//使用镜像
git clone https://git.sdut.me/Hackergeek/architecture-samples
```

## 修改下载速度

```
git config --global http.lowSpeedLimit 0
git config --global http.lowSpeedTime 999999
```

## 解决windows下每次push都要重新输入用户名和密码的问题
1. 通过"github -> account -> settings -> Developer settings -> Personal access tokens"处，点击Generate new token

2. 因为只是需要git push之类的操作，所以勾选repo选项，即可
3. 随后token生成成功，然后再在本地git bash中进行git push，账号还是原来的github账号，密码改为填写刚刚生成的token即可。之后就不会再重复输入密码了
4. 之后可以到"控制面板 -> 用户账户 -> 凭据管理器 -> Windows凭据"下查看，可以发现系统中已经保存了新的GitHub的token，此后就可以安全的使用了。