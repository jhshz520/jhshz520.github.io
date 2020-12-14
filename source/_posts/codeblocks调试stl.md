---
title: codeblocks调试stl
date: 2019-11-06 08:58:17
categories: 工具
tags: codeblocks配置
---
# 我的codeblocks是16.01
# 安装TMD-GCC
TDM-GCC-5.1.0-3下载地址：https://sourceforge.net/projects/tdm-gcc/files/TDM-GCC%20Installer/tdm-gcc-5.1.0-3.exe/download
# 改变codeblocks的Debugger配置
设置为gdb32.exe
# 在TDM 的目录bin下新建pp.gdb
```
python
import sys
sys.path.insert(0, E:\TDM-GCC-32\share\gcc-5.1.0\python\libstdcxx\v6')
from printers import register_libstdcxx_printers
register_libstdcxx_printers (None)
end
```
根据不同情况更改盘符
# 在更改codeblocks 配置中
加入调试器初始命令
```
source E:\TDM-GCC-32\bin\pp.gdb
```
# 如果不成功考虑是版本的问题，试一下32位和64位

# 还有就是在setting时将禁用GDB脚本的选项开启


# 有时在直接粘贴网页代码的时候可能会出现中文编码错误的情况

codeblocks 会报 Encoding changed的错误，这个时候一般检查一下本地的编码的开头是否存在括号，很大情况下是开头存在非ASCII编码的空格的问题需要把这个空格删除
