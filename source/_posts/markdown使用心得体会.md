---
title: markdown使用心得体会
date: 2019-09-11 17:41:24
tags: blog编辑相关
categories : 工具
---
# 标题的使用
利用#的个数来区分标题的属性
# 代码的引用
利用一对\` \` \`来实现对代码块的引用
# 编辑器的使用
vscode作为markdown的编辑器，利用组合件ctrl+shift+v来进行markdown编辑文本的预览
# 文字字体的调整
**利用一对\*\*来进行文字的加粗**

*利用一对\*来进行文字的斜体*
# 转义字符的使用
利用\\来进行特殊字符的转义
# category 文集功能

添加一个category文集标签

# 添加图片的一些注意事项

\! \[想要表述的图片名字介绍](创建的文件夹名称与编辑的md文件相同/图片名称.jpg或者图片名称.png)

特别注意此处的感叹号应该为英文输入法下的感叹号，不然会出现图片路径访问异常的错误

# 文字超链接
\!\[想要介绍的文字](想要通过该文字链接的地址)

# 有序表
\1.  数字加点之后一定要有一个空格，这样才能形成有序表


\2. \ 

## word删除空白页

选中上一页到該页用Delete键删除


## 很想知道markdown怎么使用不同颜色的字体

### 第一种形式使用KaTex/MathJax代码
```
$\color{red}{红色字}$
or
$\color{rgb(255,255,0)}{黄色字}

$\color{red}{红色字}$

$\color{yellow}{黄色字}$

$\color{green}{绿色字}$

$\color{purple}{紫色字}$

$\color{blue}{蓝色字}$

$\color{orange}{橙色}$
```

$\color{red}{红色字}$

$\color{yellow}{黄色字}$

$\color{green}{绿色字}$

$\color{purple}{紫色字}$

$\color{blue}{蓝色字}$

$\color{orange}{橙色}$

在markdown虽然能够显示出来，但是部署在github上却不能进行对应颜色的显示设置

### 使用html格式进行设置
```
<font color='red'> text </font>

<font color='red'> text </font>

<font color='red'> 红色</font>

<font color='green'>绿色</font>

<font color='pink'>粉色</font>

<font color='blue'>蓝色</font>

<font color='yellow'>黄色</font>

<font color='purple'>紫色</font>

<font color='orange'>橙色</font>
```
最后执行的结果如下

<font color='red'> text </font>

<font color='red'> 红色</font>

<font color='green'>绿色</font>

<font color='pink'>粉色</font>

<font color='blue'>蓝色</font>

<font color='yellow'>黄色</font>

<font color='purple'>紫色</font>

<font color='orange'>橙色</font>

## 怎么设置字体和大小

通过face标签设置字体，通过size标签设置文字的大小
```
<font face="黑体">我是黑体字</font>
<font face="微软雅黑">我是微软雅黑</font>
<font face="STCAIYUN">我是华文彩云</font>
<font color=#0099ff size=12 face="黑体">黑体</font>
<font color=#00ffff size=3>null</font>
<font color=gray size=5>gray</font>
```

<font face="黑体">我是黑体字</font>

<font face="微软雅黑">我是微软雅黑</font>

<font face="STCAIYUN">我是华文彩云</font>

<font color=#0099ff size=12 face="黑体">黑体</font>

<font color=#00ffff size=3>null</font>

<font color=gray size=5>gray</font>