---
title: Go语言进阶4
date: 2019-11-28 09:48:16
categories: Docker
tags: Go
---
# 主要学习系统的一些调用和文件的io操作
# os包
exec，signal，user三个子包
```
func Chdir(dir string) error   //chdir将当前工作目录更改为dir目录
func Getwd() (dir string, err error)    //获取当前目录
func Chmod(name string, mode FileMode) error     //更改文件的权限
func Chown(name string, uid, gid int) error  //更改文件拥有者owner
func Chtimes(name string, atime time.Time, mtime time.Time) error 
func Clearenv()    //清除所有环境变量（慎用）
func Environ() []string  //返回所有环境变量
func Exit(code int)     //系统退出，并返回code，其中０表示执行成功并退出，非０表示错误并退出
```
## 脚本操作
os包中StartProcess函数可以启动外部系统命令和二进制可执行文件，第一个是参数要运行的进程，第二个用来传递选项或参数，第三个是含有系统环境基本信息的结构体
，函数返回被启动进程的id，启动失败返回错误
## signal信号处理
程序执行完执行一段清理代码，进行收尾工作

os.signal包 syscall包

func Notify(c chan<- os.Signal, sig …os.Signal)

该函数会将进程收到的系统Signal转发给channel c。如果没有传入sig参数，那么Notify会将系统收到的所有信号转发给channel c
Notify函数，唤醒
# 文件系统
os包中不同函数打开（创建）文件的操作，最终还是通过函数OpenFile()来实现，而OpenFile()由编译器根据系统的情况来选择不同的底层功能来实现
```
func Mkdir(name string, perm FileMode) error 
func Chdir(dir string) error
func TempDir() string
func Rename(oldpath, newpath string) error
func Chmod(name string, mode FileMode) error
func Open(name string) (*File, error) {
	return OpenFile(name, O_RDONLY, 0)
}
func Create(name string) (*File, error) {
	return OpenFile(name, O_RDWR|O_CREATE|O_TRUNC, 0666)
}
func OpenFile(name string, flag int, perm FileMode) (*File, error) {
	testlog.Open(name)
	return openFileNolog(name, flag, perm)
}
```
```
os.Open(name string) 使用只读模式打开文件；
os.Create(name string) 创建新文件，如文件存在则原文件内容会丢失；
os.OpenFile(name string, flag int, perm FileMode) 这个函数可以指定flag和FileMode 。这三个函数都会返回一个文件对象。
```
## ioutil包
func ReadFile(filename string) ([]byte, error) // f, err := os.Open(filename)
func WriteFile(filename string, data []byte, perm os.FileMode) error  //os.OpenFile
func ReadDir(dirname string) ([]os.FileInfo, error) //	f, err := os.Open(dirname)

## io读写
io包最重要的两个接口，Reader和Writer接口

实现了这两个接口就有了I/O的功能

## 缓冲
内核中的缓冲：无论进程是否提供缓冲，内核都是提供缓冲的，系统对磁盘的读写都会提供一个缓冲（内核高速缓冲），将数据写入到块缓冲进行排队，当块缓冲达到一定的量时，才把数据写入磁盘。

进程中的缓冲：是指对输入输出流进行了改进，提供了一个流缓冲，当调用一个函数向磁盘写数据时，先把数据写入缓冲区，当达到某个条件，如流缓冲满了，或刷新流缓冲，这时候才会把数据一次送往内核提供的块缓冲中，再经块缓冲写入磁盘。

## os.File实现了Reader和Writer接口，所以在文件对象上，直接读写文件
func （f * File） Read(b []byte)(m int,err error)
func (f * File)  Write(b []byte)(n int,err error )
## 三种文件读写的方法
在使用File.Read读文件时，可考虑使用buffer：

package main

import (
	"fmt"
	"os"
)

func main() {
	b := make([]byte, 1024)
	f, err := os.Open("./tt.txt")
	_, err = f.Read(b)
	f.Close()

	if err != nil {
		fmt.Println(err)
	}
	fmt.Println(string(b))

}
二：ioutil库，没有直接实现Reader 和 Writer 接口，但是通过内部调用，也可读写文件内容：

func ReadAll(r io.Reader) ([]byte, error) 
func ReadFile(filename string) ([]byte, error)  //os.Open
func WriteFile(filename string, data []byte, perm os.FileMode) error  //os.OpenFile
func ReadDir(dirname string) ([]os.FileInfo, error)  // os.Open
三：使用bufio库，这个库实现了I/O的缓冲操作，通过内嵌io.Reader、io.Writer接口，新建了Reader ，Writer 结构体。同时也实现了Reader 和 Writer 接口。

type Reader struct {
	buf          []byte
	rd           io.Reader // reader provided by the client
	r, w         int       // buf read and write positions
	err          error
	lastByte     int
	lastRuneSize int
}

type Writer struct {
	err error
	buf []byte
	n   int
	wr  io.Writer
}


func (b *Reader) Read(p []byte) (n int, err error) 
func (b *Writer) Write(p []byte) (nn int, err error) 