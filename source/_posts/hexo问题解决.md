---
title: hexo问题解决
date: 2019-10-28 09:11:55
categories: 工具
tags: 博客相关
---
# hexo clean

很多情况下自定义的模板不能够被响应，因此采用clean清除缓存

# 清除缓存 ,网页正常情况下可以忽略此条命令,执行该指令后,会删掉站点根目录下的public文件夹

# hexo 怎么增加一个动态的人物在自己的主页面呢？


## hexo 添加看板娘
 npm install --save hexo-helper-live2d

# config配置
config中的配置是很严的必须严格对齐
## live2d
live2d:
   enable: true
   scriptFrom: local
   pluginRootPath: live2dw/
   pluginJsPath: lib/
   pluginModelPath: assets/
   tagMode: false
   debug: false
   model:
       use: live2d-widget-model-z16
       scale: 1
       hHeadPos: 0.5
       vHeadPos: 0.618
   display:
       superSample: 2
       width: 150
       height: 300
       position: right
       hOffset: 0
       vOffset: -20
   mobile:
       show: true
       scale: 0.5
  react:
      opacityDefault: 0.7
      opacityOnHover: 0.2
# hexo实现文件下载功能

  1 source文件夹添加download文件夹
  2 博文中增加
  /*
  [点击下载]/（/download//source.doc/）
  */
  # hexo Nunjucks Error:  [Line 18, Column 226] unexpected token: .

  博文中涉及到正则表达式，引起的错误，通常是将正则表达式的地方用代码括起来
# hexo 访问的问题
test my blog
避免域名每次被清空的方法
1利用cname这样避免github利用cname这样避免github repository中的domain每次被清空

2注意在source文件夹下建立一个CNAME文件，里面只写一个域名就能避免每次被清空这也是解决方法之一
重新更新一下repository中的settings才会生效

3 测试了好几次为什么每次都是先在githubpages上反映，
在www.jhshz.top上反映的很慢？需要刷新一下

是因为TTL是10分钟的原因吗，并不会马上部署完成还是需要一定的反应时间，但并不是每次都需要重新访问githubpages的，
部署顺序估计是先部署到githubpages，在其上面更新，然后通过域名访问时，其实是在访问githubpages

## 列表形式要谨慎使用
不然可能引起项目的报错
例如
```
1. 局部变量 局部变量在脚本或命令中定义，仅在当前shell实例中有效，其他shell启动的程序不能访问局部变量。
2. 环境变量 所有的程序，包括shell启动的程序，都能访问环境变量，有些程序需要环境变量来保证其正常运行。必要的时候shell脚本也可以定义环境变量。
3. shell变量 shell变量是由shell程序设置的特殊变量。shell变量中有一部分是环境变量，有一部分是局部变量，这些变量保证了shell的正常运行

如下形式就可能报错
        1. 局部变量 局部变量在脚本或命令中定义，仅在当前shell实例中有效，其他shell启动的程序不能访问局部变量。
        2. 环境变量 所有的程序，包括shell启动的程序，都能访问环境变量，有些程序需要环境变量来保证其正常运行。必要的时候shell脚本也可以定义环境变量。
        3. shell变量 shell变量是由shell程序设置的特殊变量。shell变量中有一部分是环境变量，有一部分是局部变量，这些变量保证了shell的正常运行
```

## hexo怎么更改版权网站的时间
```
©2016-2019 钟帅豪
```

在hexo/thems/3-hexo/_config.yml文件中

找到文末声明bottom_text

## hexo怎么进行同步？
**Hexo实现多终端同步管理**
假设我另外的机器上有相同的hexo
### 想要新建一个仓库