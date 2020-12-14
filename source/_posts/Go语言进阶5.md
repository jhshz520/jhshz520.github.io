---
title: Go语言进阶5
date: 2019-12-04 11:35:30
categories: Docker
tags: Go
---
# Go语言网络编程相关
Socket就是封装了这一套基于TCP/UDP/IP协议细节，提供了一系列套接字接口进行通信。

我们知道Socket有两种：TCP Socket和UDP Socket，TCP和UDP是协议，而要确定一个进程的需要三元组，还需要IP地址和端口。

# TCP Socket

建立网络连接的过程：TCP连接的建立需要经历客户端和服务端的三次握手过程。Go语言net包封装了系列API，服务端是标准的Listen + Accept结构 客户端使用net.Dial
或者DialTimeout进行连接建立

net 包中有TCPConn，是客户端和服务器端的交互通道，主要函数

```
func (c * TCPConn) Write(b []byte)(n int,err os.Error)
fucn (c * TCPConn) Read(b[] byte)(n int,err os.Error)
```
## 一个TCP链接的具体实现
服务端密码
```

```
