---
title: Anaconda env set
date: 2019-12-13 12:34:52
categories: python环境
tags: python
---
# Anaconda Prompt

1 conda info -e / conda env list
 查看 已安装的环境
2 conda create -n env-name python=X.X（版本）
创建 新的python环境
3 activate python2.7
激活新环境
 
经过以上三步就可以在后续进行pip安装相关的第三方依赖库
# Anaconda 换源
conda config --add channels https：//mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
```
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge 
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/msys2/
```
设置搜索时添加通道地址
```
conda config --set show_channel_urls yes
```
## pycharm中的python环境配置

在 file->settings 路径下

## pycharm 查找
ctrl+shift+f 不管用 可能是跟搜狗輸入法衝突
解決辦法
切换到搜狗输入法，输入 ：快捷键ctrl+shift+M，设置属性 
 属性设置 - 高级 - 系统功能 - 系统功能快捷键
ctrl+shift+N