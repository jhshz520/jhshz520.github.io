---
title: docker-compose学习
date: 2020-09-23 18:55:44
categories: 项目开发
tags: docker
---
# 基础知识
volumes

## sregistry项目中所用的docker-compose.yml
```
db:
  image: postgres
  environment:
   - POSTGRES_HOST_AUTH_METHOD=trust

uwsgi:
  restart: always
  image: quay.io/vanessa/sregistry
  env_file:
    - ./.minio-env
  volumes:
    - .:/code
    - ./static:/var/www/static
    - ./images:/var/www/images
    # uncomment for PAM auth
    #- /etc/passwd:/etc/passwd 
    #- /etc/shadow:/etc/shadow
  links:
    - minio
    - redis
    - db

nginx:
  restart: always
  image: quay.io/vanessa/sregistry_nginx
  ports:
    - "80:80"
  volumes:
    - ./nginx.conf:/etc/nginx/conf.d/default.conf:ro
    - ./uwsgi_params.par:/etc/nginx/uwsgi_params.par:ro
  volumes_from:
    - uwsgi
  links:
    - uwsgi
    - db

redis:
  restart: always
  image: redis:latest

scheduler:
  image: quay.io/vanessa/sregistry
  command: python /code/manage.py rqscheduler
  volumes:
    - .:/code
  volumes_from:
    - uwsgi
  env_file:
    - ./.minio-env
  links:
    - minio
    - redis
    - db

worker:
  image: quay.io/vanessa/sregistry
  command: python /code/manage.py rqworker default
  volumes:
    - .:/code
  volumes_from:
    - uwsgi
  env_file:
    - ./.minio-env
  links:
    - minio
    - redis
    - db

minio:
  image: minio/minio
  volumes:
    - ./minio-images:/images
  env_file:
   - ./.minio-env
  ports:
   - "9000:9000"  
  command: ["server", "images"]
```


## docker-compose down 

不仅仅是停止容器而且会对这些容器进行删除操作

停用移除所有容器以及网络相关

参数相关
```
–rmi type，删除镜像，类型必须是：all，删除compose文件中定义的所有镜像；local，删除镜像名为空的镜像
-v, –volumes，删除已经在compose文件中定义的和匿名的附在容器上的数据卷
–remove-orphans，删除服务中没有在compose中定义的容器
```

所以在停止项目进行重新修改的时候，应该利用docker-compose stop

## ubuntu进行截屏

```
sudo add-apt-repository ppa:shutter/ppa

sudo apt-get update
sudo apt-get install shutter
```

设置键盘快捷键
1. 打开系统设置
2. 打开 Keyboard 键盘设置
3. 添加成功的状态

## 怎么设置docker-compose启动的容器的名称不是随机的名称