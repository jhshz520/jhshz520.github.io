---
title: sregistry部署
date: 2020-09-16 18:25:32
categories: 网站开发
tags: 工具
---
# 安装相关依赖
## 安装docker和docker compose

```
https://docs.docker.com/engine/install/ubuntu/

```
安装docker compose
```
sudo curl -L "https://github.com/docker/compose/releases/download/1.27.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

sudo chmod +x /usr/local/bin/docker-compose

sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose

```
检验docker compose 是否已经安装成功

```
docker-compose --version
```

## 安装主机依赖
说明文档上给出的所需要的依赖环境

```
anyjson
coreapi==2.3.3
cython
django-chosen
django-crispy-forms
django-datatables-view
django-dirtyfields
django-extensions
django-filter
django-form-utils
django-gravatar2
django-guardian
django-hstore==1.3.5
django-notifications-hq
django-ratelimit==2.0.0
django-rest-swagger
django-rq
django-taggit
django-taggit-templatetags
django-user-agents
django==2.2.13
djangorestframework==3.10.3
google
google-api-python-client
h5py
ipython
markdown
numexpr
oauth2client==3.0
Pillow
PyYAML==5.1
psycopg2-binary
pygments
python3-saml
python-social-auth
requests
requests-oauthlib
requests-toolbelt
retrying
rq-scheduler
shapely
social-auth-app-django
social-auth-core[saml]
sregistry[all-basic]>=0.2.19
uwsgi
minio==5.0.8

```

查看pip是否已经安装成功

```
pip --version
```
利用pip进行相关python库的安装
真么查询都已经安装了哪些python库呢？
```
pip list
```
能够显示出pip所安装的所有python库

可以先进行容器的拉取和部署再根据报错信息进行相关依赖的补充安装
```
docker-compose up
```

可以安装Anaconda3 进行相关python库的管理和django的安装

## 下载所需的工程仓库
```
git clone https://github.com/singularityhub/sregistry
cd sregistry

git clone https://github.com/jhshz520/gridshub.git
cd gridshub
```
## 

# 进行相关设置
## Github 授权设置
```

f24fe141cb652a42e494

a9f04a9706ac6771c64ae6c80c35e412b9cc5f7f

http://gridshub.cn

http://gridshub.cn/complete/github

```
# 容器的启动和删除
```
docker-compose stop
docker-compose rm
# 查看现有的镜像
docker ps
# 删除容器镜像
docker rmi quay.io/vanessa/sregistry
docker rmi quay.io/vanessa/sregistry nginx
# 通过日志查看每一个容器
docker-compose logs uwsgi

#只查看最后三十行
docker-compose logs uwsgi --tail=30 uwsgi
# 进行交互式Debug
docker ps

docker exec -it 37b0f7d1332a bash
## 或者
NAME=$(docker ps -aqf"name=sregistry_uwsgi_1")\
docker exec -it ${NAME} bash

python manage.py shell
## 也可以输入python manage.py 来查看都能有哪些参数可以调用


```
## 一些docker常用命令
docker 删除所有镜像
```
docker rmi -f${docker images -qa}
```
## 不能够利用singularity pull 来拉取镜像的原因
没有进行https的认证
## 遇到nginx不能启动常见的解决办法
查看80端口
```
sudo lsof -i:80 
```
```
netstat -apn|grep 8080

```

### 直接查看与nginx相关的进程进行关闭
```
ps -ef|grep nginx
```

```
kill -9 [pid]
```

# 在阿里云服务器上部署服务的时候拉取镜像非常慢
尝试利用阿里云进行容器的加速

在/etc/docker/ 下重新建立一个 daemon.json 文件
```
{
"registry-mirrors":["https://bkry7tdy.mirror.aliyuncs.com"],
"experimental": true
}
```
进行docker容器的重启
```
service docker restart
```

```
sudo systemctl daemon-reload
sudo systenctl restart docker
```

但是感觉效果依然不是特别的明显

删除所有存在的镜像进行重新来过

## 超级用户和管理员的添加

### 与镜像进行交互
```
方法一：
NAME=$(docker ps -aqf "name=sregistry_uwsgi_1")
docker exec -it ${NAME} bash
python manage.py shell

方法二
NAME=$(docker ps -aqf "name=sregistry_uwsgi_1")
docker exec $NAME python /code/manage.py add_superuser --username vsoch
docker exec $NAME python /code/manage.py add_admin --username vsoch

python manage.py add_superuser --username jhshz520
python manage.py add_admin --username jhshz520
```
### 进行超级用户和管理员的删除操作
```
$ python manage.py remove_superuser --username vsoch
$ python manage.py remove_admin --username vsoch

```

## 常用的新增新的team 和colletion的caozuo
```
docker exec -it sregistry_uwsgi_1 bash
python manage.py shell


from shub.apps.users.models import User
from shub.apps.main.models import Collection
user = User.objects.get(username="myuser")
collection = Collection.objects.get(name="mycollection")


from shub.apps.users.models import Team, User
team = Team.objects.get(name="myteam")
user = User.objects.get(username="myuser")
team.members.add(user)

```
# sregistry项目的架构和页面更新

## 路由路径与设置
```
 url(r"^admin/", admin.site.urls),
    url(r"^", include(base_urls)),
    url(r"^api/", include(api_urls)),
    url(r"^", include(library_urls)),  # Sylabs library API - includes v1 and v2 (damn)
    url(r"^api/schema/$", schema_view),
    url(r"^api/docs/", include_docs_urls(title=API_TITLE, description=API_DESCRIPTION)),
    url(r"^", include(main_urls)),
    url(r"^", include(user_urls)),
    url(r"^sitemap\.xml$", index, {"sitemaps": sitemaps}, name="sitemap"),
    url(r"^sitemap-(?P<section>.+)\.xml$", sitemap, {"sitemaps": sitemaps}),
    url(r"^django-rq/", include("django_rq.urls")),
```

对应的是
base和main和user文件夹中的设置

```
gridshub.cn
``` 



## shub/apps/base/templates/base

navigation.html是一个导航栏的设置



### shub/apps/base/templates/base

```


## shub/apps/base/templates/main/index.html

index 中对应着首页的四个宣传语句

Package your Analysis

需要改动

```
<font color='red'>
 
 reproducible containers 链接

 View Collections 按钮的链接
</font>

```
Visualize your containers


Request a Build
改动之处
```
contact qq邮箱地址

contact hust 按钮

learn more at https://srcc.standford.edu

```

Deploy an Analysis

改动
<font color='red'>
Deploy an Analysis 按钮
</font>


```
### shub/apps/base/templates/main/about.html
```
在hexo 中随意插入html代码，容易造成
```

## 需要更改footer中的about等链接



## 利用singularity命令行进行容器的下载

修改源码

在singularity/internal/pkg/remote/remote.go  修改第237行
```
url:="https://"+uri+"assets/config/config.prod.json"

```

将https 换成http

重新编译

添加远程节点控制，键入API验证成功


验证成功
## 安装go语言并检查
```
export VERSION=1.13 OS=linux ARCH=amd64

wget https://dl.google.com/go/go$VERSION.$OS-$ARCH.tar.gz

sudo tar -C /usr/local -xzvf go$VERSION.$OS-$ARCH.tar.gz

rm go$VERSION.$OS-$ARCH.tar.gz

```

## 设置go语言的环境变量并更新
```
$echo 'export PATH=/usr/local/go/bin:$PATH' >> ~/.bashrc &&\ source ~/.bashrc
```


## 检查go语言是否安装成功

```
sudo go env
```


## 编译singularity 源码

```
./mconfig && \
make -C builddir && \
sudo make -C builddir install
```
-C 选项的作用是指将当前工作目录转移到你所指定的位置。“M=”选项的作用是，当用户需要以某个内核为基础编译一个外部模块的话，需要在make modules 命令中加入“M=dir”，程序会自动到你所指定的dir目录中查找模块源码，将其编译，生成KO文件cd 

## 编译时可能出现的错误
```
unable to  find the libuuid,need package libuuid-devel(uuid-dev on debian/ubuntu)
```

利用apt-get 安装相关的依赖

```
sudo apt-get install uuid-dev
```

## 检查singualrity是否安装成功
```
singularity version 
```

## singularity简单命令使用测试
```
sudo singularity pull Ubuntu

Singularity search Ubuntu

Singularity pull library://sylabsed/examples/lolcow

singularity build lolcow.sif docker://godlovedc/lolcow
进行lolcow的测试
$ singularity shell lolcow_latest.sif
Singularity lolcow_latest.sif:~> whoami

shub:// URIs 是从singularity hub 中拉取或者build from

默认的library 是 ：The default library is “https://library.sylabs.io”

sudo singularity pull 

```

登录默认节点：
```
Singularity remote list
singularity remote login
```
登录其他的远程节点
```
singularity remote login <remote_name>
```

添加远程登录节点
```
singularity remote add <remote_name> <remote_uri>

singularity remote add  gridshub   http://gridshub.cn
```
移除登录节点：
```
singularity remote remove gridshub
```




# 如何利用命令行进行下载呢？
1. 进行singularity源码的修改和进行编译
2. 在命令行中新增endpoint，并设置为默认remote endpoint
3. 进行API_token的登录验证
4. 能够进行上传和下载
##  新增endpoint设置为默认
```
singularity remote add gridshub http:gridshub.cn

singularity remote use gridshub
```

## 进行登录
```
singularity remote login gridshub

将网页 gridshub.cn/token 页面的API token复制进去并认证
```

## 上传容器
```
singularity push -U ubuntu——latest.sif library://jhshz520/my/another:latest




```
<font color='orange'>
-U 表示unsined

jhshz520 表示的是用户名（user）

my 对应 collection

another：latest 表示上传之后容器的名字和tag
</font>

## 下载容器
```
singularity pull my/another:latest
```
<font color='green'>
因为已经登录进去了所以可以直接拉取某个Collection下的容器
</font>

# 数据持久化存在问题
在进行本地的运行和停止之后又被当做新用户进行重新的登录和注册