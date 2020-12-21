---
title: HPCCM
date: 2020-12-21 17:03:01
categories: 课题相关
tags: 工具
---
# HPC Container Maker

## 安装 
入门
安装,利用pip或者Conda
```
sudo pip install hpccm

sudo install -c conda-forge hpccm

```
## 使用
可以通过Python模块进行使用，hpccm 命令行接口

HPCCM 是一组routines 能够生成容器定义文件
```
#!/usr/bin/env python
import hpccm
# set to 'docker' to generate a Dockerfile or set to 'singularity' to generate a singularity definition file
hpccm.config.set_container_format('docker')

print(hpccm.primitives.baseimage(image='centos:7'))
print(hpccm.building_blocks.gnu())

```
python 模块提供很大的弹性，但是我们依然需要负责管理输入和输出


## HPCCM命令行工具
命令行工具能够处理HPCCM recipe 文件，然后生成容器定义文件

```
Stage0+=baseimage(image='centos:7')
Stage0+=gnu()
```
命令行工具使用如下：

```
hpccm --recipe <recipe_file> --format docker

hpccm --recipe <recipe_file> --format singularity
```
因为HPCCM recipes 是Python脚本也因此也能够将Python代码写入其中

## 构建容器镜像
HPCCM 的输出时一个容器定义文件，因此需要保存到文件中命名为dockerfile 或者Singularity.def

例如下列用法：
```
# docker
hpccm --recipe <recipe.py> --format docker > Dockerfile
sudo docker build -t <tag> -f Dockerfile .
# singularity 
hpccm  --recipe <recipe.py> --format singularity > Singularity.def
sudo singularity build <image_file.sif> Singularity.def
```
也可以脚本文件

```
hpccm --recipe <recipe.py> --format bash > script.sh
```

## HPCCM tutorial
容器开始的起点是一个基础镜像，这个基础镜像应该包含CUDA，CUDA基础镜像： CUDA10.2 和centos7
nvidia/cuda:10.2-devel-centos7
**一次第一行应该是定义基础镜像**
```
Stage0+= baseimage(image='nvidia/cuda:10.2-devel-centos7')
```
多阶段构建是一种重要技术能够很大程度缩小镜像的容量，因为tutorial部分没有用到多阶段构建技术，可以把Stage0当做一个模板前缀


## 使用HPCCM 构建 GROMACS,MILC 和MPI Bandwidth


## 定制化容器和API