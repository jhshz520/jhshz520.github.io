---
title: Go语言进阶2
date: 2019-11-27 09:11:00
categories: Docker
tags: Go
---
# 指针和内存
## 指针
指针变量在 32 位计算机上占用 4B 内存，在 64 位计算机占用 8B内存

符号 * 可以放在一个类型前，如 *T，那么它将以类型T为基础，生成指针类型*T。未初始化指针的值为nil

例
```
type Point3D struct{x,y,z,float64}
var pointer * Point3D
var i *[4]int
```


## 指针的反向引用
符号 * 可以放在一个指针前，如 (*pointer)，那么它将得到这个指针指向地址上所存储的值，这称为反向引用。

(*pointer).x可以简写为pointer.x。

## 指针相关的内存管理理解
内存管理中的内存区域一般包括堆内存（heap）和栈内存（stack）， 栈内存主要用来存储当前调用栈用到的简单类型数据，如string，bool，int，float 等。这些类型基本上较少占用内存，容易回收，因此可以直接复制，进行垃圾回收时也比较容易做针对性的优化。 而复杂的复合类型占用的内存往往相对较大，存储在堆内存中，垃圾回收频率相对较低，代价也较大，因此传引用或指针可以避免进行成本较高的复制操作，并且节省内存，提高程序运行效率。

## new（）和make（）的区别
两者均在堆上分配内存

new（）用于值类型的内存分配并且赋值值为零值

make（）只用于切片字典以及通道三种引用数据类型的内存分配和初始化

C++中 new（）创建的变量总是在堆上

Go中变量位置由编译器决定，编译器根据变量大小和泄露分析的结果确定位置

## 垃圾回收
runtime.GC（）

显式触发，内存资源不够用时调用runtime.GC(),在此函数执行的点上立即释放一大片内存，此时程序会有短时的性能下降

SetFinalizer（obj interface{}，finalizer interface{}）

obj必须是指针类型，finalizer是一个函数，参数类型是obj类型，没有返回值

## 查看内存情况函数
func Mem（m *runtime.Memstats）{

runtime.ReadMemstats(m)
log.Printf("%d Kb\n",m.Alloc/1024)


}

# Go的面向对象
Go实现面向对象的两个关键是struct和interface，结构代替类，因为Go语言不提供类，但提供了结构体或自定义类型，方法可以被添加到结构体或自定义类型中。结构体之间可以嵌套，类似继承。而interface定义接口，实现多态性。
## Go通过声明不定参数来实现多重继承

func Println（a...interface{}）(n int,err error){
    return Fprintln(os.Stdout,a)
}

# reflect(反射)
反射是应用程序检查其所拥有的结构，尤其是能用来检查类型的一项功能
## reflect包
reflect.TypeOf()
检查类型
reflect.ValueOf()
检查值
```
func TypeOf（i interface{}） Type
type Type interface


func ValueOf（i interface{}） Value
type Value struct
```

Go语言实现中，interface类型变量存储了两个信息，值类型对
```
(value,type)
```
## reflect.Type

Kind()返回一个常量，只能用于返回指针，数组，切片，字典，通道的基类型，其他方法会panic

## reflect.Value

Type() 返回类型所对应的静态类型，Kind（）返回一个常量，表示具体类型的底层类型

在Go语言中，类型包括 static type和concrete type. 简单说 static type是你在编码是看见的类型(如int、string)，concrete type是实际具体的类型，runtime系统看见的类型

## 通过反射可以修改原对象
### d.CanAdddr()方法：判断是否可以被取地址
### d.CanSet（）：判断它是否可被取地址并可被修改


## 结构体中有tag标签，通过反射可获取结构体成员变量的tag信息
```
package main

import (
	"fmt"
	"reflect"
)

type Student struct {
	name string
	Age  int `json:"years"`
}

func main() {
	var Pupil Student = Student{"joke", 18}
	setStudent := reflect.ValueOf(&Pupil).Elem()

	sSAge, _ := setStudent.Type().FieldByName("Age")
	fmt.Println(sSAge.Tag.Get("json")) // years
}
```
# unsafe包
三个函数两个类型
```
func Alignof(x ArbitraryType) uintptr
func Offsetof(x ArbitraryType) uintptr
func Sizeof(x ArbitraryType) uintptr
type ArbitraryType int
type Pointer *ArbitraryType
```

Go 语言的指针类型长度与int类型长度，在内存中占用的字节数是一样的。ArbitraryType类型的变量也可以是指针

Alignof返回变量对齐字节数量
Offsetof返回变量指定属性的偏移量，所以如果变量是一个struct类型，不能直接将这个struct类型的变量当作参数，只能将这个struct类型变量的属性当作参数。
Sizeof 返回变量在内存中占用的字节数，切记，如果是slice，则不会返回这个slice在内存中的实际占用长度。

利用以上的类型和方法可以实现在内存地址映射中的来回游走

## 指针运算
ointer不能参与指针运算要靠uintptr
Pointer类型先转换成uintptr类型，做完地址加减法运算后，再转换成Pointer类型，通过*操作达到取值、修改值的目的


unsafe.Pointer其实就是类似C的void *，在Go 语言中是用于各种指针相互转换的桥梁，也即是通用指针。

uintptr 无法持有对象， uintptr 类型的目标会被回收；

unsafe.Pointer 可以和 普通指针 进行相互转换