---
title: conda基本使用
date: 2020-09-21 08:04:28
categories: 工具
tags: Anaconda
---
# Anaconda3 安装
```
https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/
```
下载链接

利用清华的源下载
然后进行安装
```
bash Anaconda3-5.3.1-linux-x86_64.sh
```

# 查看所有环境
```
conda env list

conda info --envs
```

# 激活和退出一个环境
```
conda activate base

conda deactivate

如果报错：可以尝试 source activate
source deactivate
```

## 创建任意版本的python环境
```
conda create --name python362 python=3.6.2

```
## 复制一个环境
```
conda create --name new_python362 --clone python362
``` 
创建的环境都在D：\Anaconda3\envs
## 删除环境
```
conda remove --name python362 --all
```
## conda环境下安装和卸载包
```
conda install requests

conda remove requests

conda install --name python362 requests// 指定环境中安装包

conda remvoe --name python362 requests

conda search --full name pandas // 查找一个包及其所有版本

conda update --all 更新所有包

conda update 包名

也可以使用pip 进行安装和卸载包，但pip只是包管理工具，conda是环境管理，可以在指定环境下进行安装和卸载


```
## conda的换源
```
conda config --add channels https://mirros.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
conda config --add channels https://mirros.tuna.tsinghua.end.cn/anaconda/pkgs/main/
conda config --set show_channel_urls yes
```
## 我的环境创建实例
```
conda create --name shub python=3.6.2
启用环境source activate shub
退出环境 source deactivate


```

## 进入环境根据提示缺少什么依赖安装什么 依赖的版本见requirenments.txt
### 批量安装
```
pip3 install -r requirements.txt 
```
### gnupg:破坏 software-properties-common（<=0.96.24.3） but
```
sudo apt-get install --only-upgrade gnupg
```
ubuntu 16.04 千万不要贸然升级内核，因为升级完内核之后有很多的问题，最常见的就是不能正常进入ubuntu的桌面系统，进入恢复模式也仅仅是用心的内核还需要安装显卡驱动等等十分的麻烦，避免不了重装系统的后果

