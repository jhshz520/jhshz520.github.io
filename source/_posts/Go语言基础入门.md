---
title: Go语言基础入门
date: 2019-11-16 17:07:53
categories: Docker
tags: Go
---
# windows 下的开发IDE 
GoLand
# 直接用jetbrains教育网账号激活

# 基本数据类型

(a) 指针类型（Pointer） (b) 数组类型 (c) 结构类型(struct) (d) Channel 类型 (e) 函数类型 (f) 切片类型 (g) 接口类型（interface） (h) Map 类型

# 标识符（identifiers）
1. 下划线也被认为是字母

# 关键字（keywords）
 break default func interface select case

 defer go map package switch const

 fallthrough if range type

 continue for import return var


变量声明使用关键字var
例
```
var(
    a int 
    b bool
    str string
    浮点 float32 //中文也是可以作为变量标识符的，简直不要太爽
)
```


一个变量var 声明之后，系统自动赋予它该类型的零值
 
 ## 下面是各种变量以及其对应的零值

 1. int 0
 2. float 0.0
 3. bool false
 4. string 空字符串
 5. 指针 nil
##  多变量可以在同一行进行赋值，称为并行赋值
例
```
a,b,c = 5, 7 ,"abc"


简式声明：

a,b,c := 5,7,"abc"
```
简式声明一般用在func内，注意 全局变量和简式声明的变量不要同名，否则容易产生偶然的变量隐藏

反例
```
func main() {  
    x := 1
    fmt.Println(x)     // prints 1
    {
        fmt.Println(x) // prints 1
        x := 2
        fmt.Println(x) // prints 2
    }
    fmt.Println(x)     // prints 1 (不是2)
}
```
### 交换变量不用再用交换函数了
a,b = b,a
### 空白标识符常常用来抛弃值
例：
```
_,b= 5,7

```

# 标点符号（punctuation）

# 字面量（literals）



####################################################


#  左位移与iota计数配合实现存储单位的常量枚举
```
type Bytesize float64;
	const(
		KB Bytesize  = 1 <<(10*iota)
		MB
		GB
		TB
		PB
	)
	fmt.Print(KB,MB,GB,TB,PB)
```
# Go语言约定规则
### 可见性规则
标识符必须以一个大写字母开头，这样才可以被外部包的代码所使用，这被称为导出。标识符如果以小写字母开头，则对包外是不可见的，但是他们在整个包的内部是可见并且可用的。但是包名不管在什么情况下都必须小写
### 命名规范和语法惯例
1. 命名
当某个函数需要被外部包调用的时候需要使用大写字母开头，并遵循 Pascal 命名法（“大驼峰式命名法”）；否则就遵循“小驼峰式命名法”，即第一个单词的首字母小写，其余单词的首字母大写。

单词之间不以空格断开或连接号（-）、底线（_）连结，第一个单词首字母采用大写字母；后续单词的首字母亦用大写字母，例如：FirstName、LastName。每一个单词的首字母都采用大写字母的命名格式，被称为“Pascal命名法”，源自于Pascal语言的命名惯例，也有人称之为“大驼峰式命名法”（Upper Camel Case），为驼峰式大小写的子集。

当二个或二个以上单词连结在一起时，用驼峰式命名法可以增加变量和函数名称的可读性。
2. 分号的使用也使用分号作为语句的结束，但一般会省略分号。像在标识符后面；整数、浮点、复数、Rune或字符串等字面量后面；关键字break、continue、fallthrough、或者return后面；操作符或标点符号++、--、)、]或}之后等等都可以使用分号，但是往往会省略掉，像LiteIDE编辑器会在保存.go文件时自动过滤掉这些分号，所以在Go语言开发中一般不用过多关注分号的使用。
3. 左大括号 { 不能单独一行，这是编译器的强制规定，否则你在使用 gofmt 时就会出现错误提示“ expected declaration, found '{' ”。右大括号 } 需要单独一行。
4. 在定义接口名时也有惯例，一般单方法接口由方法名称加上-er后缀来命名
## 注释
1. 行注释,使用双斜线//开始，一般后面紧跟一个空格。行注释是Go语言中最常见的注释形式，在标准包中，一般都采用行注释，建议采用这种方式。
2. 块注释：使用 /* */，块注释不能嵌套。块注释一般用于包描述或注释成块的代码片段。
注释文字尽量每行长度接近一致，过长的行应该换行以方便在编辑器阅读。注释可以是单行，多行，甚至可以使用doc.go文件来专门保存包注释。每个包只需要在一个go文件的package关键字上面注释，两者之间没有空行。

对于变量，函数，结构体，接口等的注释直接加在声明前，注释与声明之间没有空行。

3. 函数或方法的注释需要以函数名开始，且两者之间没有空行
4. 在方法，结构体或者包注释前面加上“Deprecated:”表示不建议使用

在注释中，还可以插入空行
# 代码结构化
## 包的概念
Go语言中包的主要作用是把功能相似或相关的代码组织在同一个包中，以方便查找和使用

每个.go文件都必须归属于某一个包，每个文件都可有init()函数。包名在源文件中第一行通过关键字package指定，包名要小写
## 包的导入
一个Go程序通过import关键字将一组包链接在一起。import其实是导入目录，而不是定义的包名称，实际应用中我们一般都会保持一致

当导入多个包时，一般按照字母顺序排列包名称，像LiteIDE会在保存文件时自动完成这个动作。所谓导入包即等同于包含了这个包的所有的代码对象。

为避免名称冲突，同一包中所有对象的标识符必须要求唯一。但是相同的标识符可以在不同的包中使用，因为可以使用包名来区分它们。
## 三种特殊的import方式

### 点操作的含义是某个包导入之后，在调用这个包的函数时，可以省略前缀的包名，如这里可以写成Println("Hello World!")，而不是fmt.Println("Hello World!")。例如：

import( . "fmt" ) 
### 别名操作
别名操作就是可以把包命名成另一个容易记忆的名字。例如：

import(
    f "fmt"
)
别名操作调用包函数时，前缀变成了别名，即f.Println("Hello World!")。在实际项目中有时这样使用，但请谨慎使用，不要不加节制地采用这种形式。

# 引入包但是不直接使用包里的函数，只调用该包里面的init函数
_ 操作是引入某个包，但不直接使用包里的函数，而是调用该包里面的init函数，比如下面的mysql包的导入。此外在开发中，由于某种原因某个原来导入的包现在不再使用，也可以采用这种方式处理，比如下面fmt的包。代码示例如下：

import (
	_ "fmt"
	_ "github.com/go-sql-driver/mysql"
)
## 标准库
在 Go 的安装文件里包含了一些可以直接使用的标准库。在GOROOT/src中可以看到源码，也可以根据情况自行重新编译。

完整列表可以访问GoWalker（https://gowalker.org/）查看。
unsafe: 包含了一些打破 Go 语言“类型安全”的命令，一般的程序中不会被使用，可用在 C/C++ 程序的调用中。
```
    syscall-os-os/exec:
    	os: 提供给我们一个平台无关性的操作系统功能接口，采用类UNIX设计，隐藏了不同操作系统间差异，让不同的文件系统和操作系统对象表现一致。
    	os/exec: 提供我们运行外部操作系统命令和程序的方式。
    	syscall: 底层的外部包，提供了操作系统底层调用的基本接口。
    archive/tar 和 /zip-compress：压缩(解压缩)文件功能。
    fmt-io-bufio-path/filepath-flag:
    	fmt: 提供了格式化输入输出功能。
    	io: 提供了基本输入输出功能，大多数是围绕系统功能的封装。
    	bufio: 缓冲输入输出功能的封装。
    	path/filepath: 用来操作在当前系统中的目标文件名路径。
    	flag: 对命令行参数的操作。　　
    strings-strconv-unicode-regexp-bytes:
    	strings: 提供对字符串的操作。
    	strconv: 提供将字符串转换为基础类型的功能。
    	unicode: 为 unicode 型的字符串提供特殊的功能。
    	regexp: 正则表达式功能。
    	bytes: 提供对字符型分片的操作。
    math-math/cmath-math/big-math/rand-sort:
    	math: 基本的数学函数。
    	math/cmath: 对复数的操作。
    	math/rand: 伪随机数生成。
    	sort: 为数组排序和自定义集合。
    	math/big: 大数的实现和计算。 　　
    container-/list-ring-heap: 实现对集合的操作。
    	list: 双链表。
    	ring: 环形链表。
   time-log:
        time: 日期和时间的基本操作。
        log: 记录程序运行时产生的日志。
    encoding/Json-encoding/xml-text/template:
        encoding/Json: 读取并解码和写入并编码 Json 数据。
        encoding/xml:简单的 XML1.0 解析器。
        text/template:生成像 HTML 一样的数据与文本混合的数据驱动模板。
    net-net/http-html:
        net: 网络数据的基本操作。
        http: 提供了一个可扩展的 HTTP 服务器和客户端，解析 HTTP 请求和回复。
        html: HTML5 解析器。
    runtime: Go 程序运行时的交互操作，例如垃圾回收和协程创建。
    reflect: 实现通过程序运行时反射，让程序操作任意类型的变量
```
## 导入外部安装包
### go install
可以使用go install在你的本地机器上安装它们。go install 是Go语言中自动包安装工具：如需要将包安装到本地它会从远端仓库下载包：检出、编译和安装一气呵成。

go install/build都是用来编译包和其依赖的包。

go install 使用了 GOPATH 变量

假设你想使用https://github.com/gocolly/colly 这种托管在 Google Code、GitHub 和 Launchpad 等代码网站上的包。

你可以通过如下命令安装： go install github.com/gocolly/colly 将一个名为 github.com/gocolly/colly 安装在GOPATH/pkg/ 目录下。

区别： go build只对main包有效，在当前目录编译生成一个可执行的二进制文件（依赖包生成的静态库文件放在GOPATH/pkg）。

go install一般生成静态库文件放在GOPATH/pkg目录下，文件扩展名a。

## 包的init（）初始化
可执行应用程序的初始化和执行都起始于main包。如果main包的源代码中没有包含main()函数，则会引发构建错误 undefined: main.main。main()函数既没有参数，也没有返回类型，init()函数和main()函数在这一点上两者一样

等所有被导入的包都加载完毕了，就会开始对main包中的包级常量和变量进行初始化，然后执行main包中的init()函数，最后执行main()函数。

Go语言中init()函数常用于包的初始化
### init（）函数的特征

init函数是用于程序执行前做包的初始化的函数，比如初始化包里的变量等
每个包可以拥有多个init函数
包的每个源文件也可以拥有多个init函数
同一个包中多个init()函数的执行顺序不定
不同包的init()函数按照包导入的依赖关系决定该函数的执行顺序
init()函数不能被其他函数调用，其在main函数执行之前，自动被调用
## 项目结构
### 使用godoc
godoc工具会收集这些注释并产生一个技术文档

命令行下进入目录下并输入命令： godoc -http=:6060 -goroot="."

然后在浏览器打开地址：http://localhost:6060

然后你会看到本地的 Godoc 页面，从左到右一次显示出目录中的包
### Go程序的编译

编译有关的命令主要是go run ,go build , go install这三个命令

go run只能作用于main包文件，先运行compile 命令编译生成.a文件，然后 link 生成最终可执行文件并运行程序，这过程的产生的是临时文件，在go run 退出前会删除这些临时文件（含.a文件和可执行文件）。最后直接在命令行输出程序执行结果。go run 命令在第二次执行的时候，如果发现导入的代码包没有发生变化，那么 go run 不会再次编译这个导入的代码包，直接进行链接生成最终可执行文件并运行程序

go install用于编译并安装指定的代码包及它们的依赖包，并且将编译后生成的可执行文件放到 bin 目录下（GOPATH/bin），编译后的包文件放到当前工作区的 pkg 的平台相关目录下。

go build用于编译指定的代码包以及它们的依赖包。如果用来编译非main包的源码，则只做检查性的编译，而不会输出任何结果文件。如果是一个可执行程序的源码（即是 main 包），这个过程与go run 大体相同，除了会在当前目录生成一个可执行文件外。

使用go build时有一个地方需要注意，对外发布编译文件如果不希望被人看到源代码，请使用go build -ldflags 命令，设置编译参数-ldflags "-w -s" 再编译后发布。避免使用gdb来调试而清楚看到源代码
## 运算符
 ^ 运算符表示的是按位异或
 && 逻辑与
 & 按位与
### 左移n位表示乘以2的n次方，高位丢弃，低位补零

### &^将运算符左边数据相异的位保留，相同位清除
# string
Go语言的range循环在处理字符串的时候，会自动隐式解码UTF8字符串

```
func main() {

	s := "其实就是rune"
	fmt.Println(len(s))                    // "16"
	fmt.Println(utf8.RuneCountInString(s)) // "8"
}
```
### 字符串拼接

1. 由于编译器行尾自动补全分号的缘故，加号 + 必须放在第一行。 拼接的简写形式 += 也可以用于字符串
2. strings.Join()

``` 
strings.Join([]string{"hello", "world"}, ", ")
```
Join会先根据字符串数组的内容，计算出一个拼接之后的长度，然后申请对应大小的内存，一个一个字符串填入，在已有一个数组的情况下，这种效率会很高，但是本来没有，去构造这个数据的代价也不小。
1. bytes.Buffer

```
var buffer bytes.Buffer
buffer.WriteString("hello")
buffer.WriteString(", ")
buffer.WriteString("world")

fmt.Print(buffer.String())
```
这个比较理想，可以当成可变字符使用，对内存的增长也有优化，如果能预估字符串的长度，还可以用 buffer.Grow() 接口来设置 capacity。
4. string.Builder
```
var b1 strings.Builder
b1.WriteString("ABC")
b1.WriteString("DEF")

fmt.Print(b1.String())
```
strings.Builder 内部通过 slice 来保存和管理内容。slice 内部则是通过一个指针指向实际保存内容的数组。strings.Builder 同样也提供了 Grow() 来支持预定义容量。当我们可以预定义我们需要使用的容量时，strings.Builder 就能避免扩容而创建新的 slice 了。strings.Builder是非线程安全，性能上和 bytes.Buffer 相差无几。


### 里面的字符串都是不可变的，每次运算都会产生一个新的字符串，所以会产生很多临时的无用的字符串，不仅没有用，还会给 GC 带来额外的负担，所以性能比较差。

# 有关string处理的包
标准库中有四个包对字符串处理尤为重要：bytes、strings、strconv和unicode包

strings包提供了许多如字符串的查询、替换、比较、截断、拆分和合并等功能。

bytes包也提供了很多类似功能的函数，但是针对和字符串有着相同结构的[]byte类型。因为字符串是只读的，因此逐步构建字符串会导致很多分配和复制。在这种情况下，使用bytes.Buffer类型将会更有效，稍后我们将展示。

strconv包提供了布尔型、整型数、浮点数和对应字符串的相互转换，还提供了双引号转义相关的转换。

unicode包提供了IsDigit、IsLetter、IsUpper和IsLower等类似功能，它们用于给字符分类。

strings 包提供了很多操作字符串的简单函数，通常一般的字符串操作需求都可以在这个包中找到
# slice切片

切片（slice） 是对底层数组一个连续片段的引用，所以切片是一个引用类型。切片提供对该数组中编号的元素序列的访问。未初始化切片的值为nil。

与数组一样，切片是可索引的并且具有长度。切片s的长度可以通过内置函数len() 获取;与数组不同，切片的长度可能在执行期间发生变化。元素可以通过整数索引0到len（s）-1来寻址。我们可以把切片看成是一个长度可变的数组。

切片提供了计算容量的函数 cap() ，可以测量切片最大长度。切片的长度永远不会超过它的容量，所以对于切片 s 来说，这个不等式永远成立：0 <= len(s) <= cap(s)。

一旦初始化，切片始终与保存其元素的基础数组相关联。因此，切片会和与其拥有同一基础数组的其他切片共享存储;相比之下，不同的数组总是代表不同的存储。

切片下面的数组可以延伸超过切片的末端。容量是切片长度与切片之外的数组长度的总和。

使用内置函数make()可以给切片初始化，该函数指定切片类型和指定长度和可选容量的参数

## 切片相较于数组的优点
因为切片是引用，所以它们不需要使用额外的内存并且比使用数组更有效率，所以在 Go 代码中切片比数组更常用

切片格式 var identifier []type,一个切片在未初始化之前默认是nil，长度为0

切片的初始化格式
```
var slice1 []type = arr1[start:end]
```

类似数组初始化的方式

```
var x = []int{2,3,5,7,9}
```


make()函数创建切片，同时创建相关数组：
```
var slice1 []type = make([]type,len,cap)
```

简写为 
```
v:=make([]int,10,50)
```

## slice 重组
slice1= slice2[0:end]


copy（）函数避免空间的浪费
```
package main

import "fmt"

func get() []byte {
	raw := make([]byte, 10000)
	fmt.Println(len(raw), cap(raw), &raw[0]) // 显示: 10000 10000 数组首字节地址
	res := make([]byte, 3)
	copy(res, raw[:3]) // 利用copy 函数复制，raw 可被GC释放
	return res
}

func main() {
	data := get()
	fmt.Println(len(data), cap(data), &data[0]) // 显示: 3 3 数组首字节地址
}

```
### append()追加

append()函数将 0 个或多个具有相同类型S的元素追加到切片s后面并且返回新的切片；追加的元素必须和原切片的元素同类型。如果s的容量不足以存储新增元素，append()会分配新的切片来保证已有切片元素和新增元素的存储。

因此，append()函数返回的切片可能已经指向一个不同的相关数组了。append()函数总是返回成功，除非系统内存耗尽了

append()函数操作如果导致分配新的切片来保证已有切片元素和新增元素的存储，也就是返回的切片可能已经指向一个不同的相关数组了，那么新的切片已经和原来切片没有任何关系，即使修改了数据也不会同步。

append()函数操作后，有没有生成新的切片需要看原有切片的容量是否足够


**最重要的是看一下底层数组是否还是原来的底层数组**

# 字典map
```
var map1 map[keytype]valuetype
```
在声明的时候不需要知道 map 的长度，map 是可以动态增长的

map 可以用 {key1: val1, key2: val2} 的描述方法来初始化，就像数组和结构体一样

map 容量： 和数组不同，map 可以根据新增的 key-value 对动态的伸缩，因此它不存在固定长度或者最大限制。
```
package main
func main() {  
    var s []int
    s = append(s, 1)
}
```
会发生错误：
```
package main
func main() {  
    var m map[string]int
    m["one"] = 1 // 错误

}
```
## map 的一些初始化操作
```
// 声明但未初始化map，此时是map的零值状态
map1 := make(map[string]string, 5)

map2 := make(map[string]string)

// 创建了初始化了一个空的的map，这个时候没有任何元素
map3 := map[string]string{}

// map中有三个值
map4 := map[string]string{"a": "1", "b": "2", "c": "3"}

```
## map中key值的访问
```
val1,isPresent := map1[key1]
或者
val1 = map1[key1]
``` 
## map中判断某个key值是否存在
```
if_,ok :=X["two"];!ok{
	fmt.Println("no entry")
}
```
## map的删除操作
delete（map1，key1）
delete（map3，"a"）
即使key1不存在，该操作也不会产生错误
## range的使用
range 语句中的值是真实集合元素的拷贝，不是原有元素的引用，更新range中的值并不会修改原来的数据，同时使用这些值的地址也并不会得到原有数据的指针
```
package main
import "fmt"
func main(){
	data := []int{1,3,2}
	for_,v :=range data{
		v*=10
	}
	fmt.Println("data",data)//元数据并不会更新
}
```

**如果想要更新原有集合中的数据，使用索引操作符来获得数据**
```
package main
import "fmt"
func main(){
	data := [] int {1,2,3}
	for i，_:=range data{
		data[i]*=10
	}
	fmt.Println("data",data)//data [10,20.30]
}
```

# 流程控制
## switch
```
switch var1{
	case val1:
'''''
    case val2:
'''''
   default:
'''''



switch {
    case condition1:
        ...
    case condition2:
        ...
    default:
        ...
}


}
```
switch 语句的第二种形式是不提供任何被判断的值（实际上默认为判断是否为 true）

```
switch initialization {
    case val1:
        ...
    case val2:
        ...
    default:
        ...
}
switch result := calculate(); {
    case result < 0:
        ...
    case result > 0:
        ...
    default:
        // 0
}
```
switch 的第三中形式中包含一个初始化的语句

正常来说，一旦成功匹配到某一个分支语句时，执行完相应的操作就会退出整个switch代码块


但是可以通过fallthrough强制执行后面的case代码，而且最最关键的是fallthrough不会判断下一条case的结果是否是真，直接执行

## select 控制

select控制主要用于处理异步通道操作，所有的情况都会涉及通信操作，select会监听分支语句中的通道的读写操作，当分支中的通道读写操作为非阻塞状态时，将会触发相应的动作select语句会选择一组可以发送和接受的操作中的一个分支继续执行。select没有条件表达式，一直在等待分支进入可运行的状态

**select中的case语句必须是一个channel操作**
**select中的default子句总是可运行的**

如果有多个分支都可以运行，select会伪随机公平地选出一个执行，其他分支不会执行。
如果没有可运行的分支，且有default语句，那么就会执行default的动作。
如果没有可运行的分支，且没有default语句，select将阻塞，直到某个分支可以运行。
```
package main

import (
	"fmt"
	"time"
)

func main() {
	var c1, c2, c3 chan int
	var i1, i2 int
	select {
	case i1 = <-c1:
		fmt.Printf("received ", i1, " from c1\n")
	case c2 <- i2:
		fmt.Printf("sent ", i2, " to c2\n")
	case i3, ok := (<-c3): 
		if ok {
			fmt.Printf("received ", i3, " from c3\n")
		} else {
			fmt.Printf("c3 is closed\n")
		}
	case <-time.After(time.Second * 3): //超时退出
		fmt.Println("request time out")
	}
}
```
## for循环
最简单的基于计数器的迭代，基本形式
for 初始化语句;条件语句;修饰语句{}

因为Go语言有平行赋值的特性，因此可以在循环中使用多个计数器
```
for i,j := 0,N;i <j;i,j = i+1,j-1{}

```
条件语句可以被省略，但是

如果 for 循环的头部没有条件语句，那么就会认为条件永远为 true，因此循环体内必须有相

关的条件判断以确保会在某个时刻退出循环。
## for-range结构
是Go语言特有的一种迭代机构，可以迭代任何一个集合，包括数组和字典同时可以获得每次迭代相应的索引和值，一般形式：
```
for ix,val := reange coll{}
```
特别注意的是
**val是集合中对应索引的值的副本，一般只有只读性质**
对于val所做的任何的修改都不会影响实际的集合中的值

除非val是指针
```
package main

import (
	"fmt"
	"time"
)

type field struct {
	name string
}

func (p *field) print() {
	fmt.Println(p.name)
}

func main() {
	data := []field{ {"one"}, {"two"}, {"three"} }

	for _, v := range data {
		go v.print()
	}
	time.Sleep(3 * time.Second)
	// goroutines （可能）显示: three, three, three
}
```
## 当前的迭代变量作为匿名goroutine的参数
```
package main

import (  
    "fmt"
    "time"
)

func main() {  
    data := []string{"one", "two", "three"}

    for _, v := range data {
        go func(in string) {
            fmt.Println(in)
        }(v)
    }

    time.Sleep(3 * time.Second)
    // goroutines输出: one, two, three
}
```
### Unicode编码的字符串，可以用for range来进行迭代
```
for pos ,char  := range str{

}
```
if语句由布尔表达式后跟一个或多个语句组成

break

continue 与C++相同

### label 
label可以与for switch 或者select语句配合使用，continue语句指向LABEL1，当执行到该语句的时候，会跳转到LABEL1标签的位置

LABEL 和GOTo语句不鼓励使用，很快会导致非常糟糕的程序设计，可以有更加易读的代码方案来进行代替

# 错误处理
## 错误类型
导入errors包

errors.New函数用来接收合适的错误信息
```
err := errors.New("math - square root of negative number")
func Sqrt(f float64) (float64, error) {
if f < 0 {
        return 0, errors.New ("math - square root of negative number")
    }
}
```
也可以使用fmt创建错误对象
```
if f < 0 {
    return 0, fmt.Errorf("square root of negative number %g", f)
}
```
## panic

panic()是内置函数，表示非常严重不可恢复的错误，先声明defer，不然捕获不到异常

发生异常时，开始运行defer，defer处理完再返回

在多层嵌套的函数调用中调用 panic()，可以马上中止当前函数的执行，所有的 defer 语句都会保证执行并把控制权交还给接收到异常的函数调用者。这样向上冒泡直到最顶层，并执行（每层的） defer，在栈顶处程序崩溃，并在命令行中用传给异常的值报告错误情况：这个终止过程就是 panicking

一般不要随意用 panic() 中止程序，必须尽力补救错误让程序能继续执行

自定义包中的错误处理和 panicking，这是所有自定义包实现者应该遵守的最佳实践：

1）在包内部，总是应该从异常中 recover：不允许显式的超出包范围的 panic()

2）向包的调用者返回错误值。
### recover（）函数的调用仅仅当他在defer函数中被直接调用才有效
```
package main

import (
	"fmt"
)

func div(a, b int) {

	defer func() {

		if r := recover(); r != nil {
			fmt.Printf("捕获到异常：%s\n", r)
		}
	}()

	if b < 0 {

		panic("除数需要大于0")
	}

	fmt.Println("余数为：", a/b)

}

func main() {
	// 捕捉内部的异常
	div(10, 0)

	// 捕捉主动的异常
	div(10, -1)
}

程序输出：

捕获到异常：runtime error: integer divide by zero
捕获到异常：除数需要大于0
```
## Recover()异常恢复
recover() 这个内建函数被用于从异常或错误场景中恢复：让程序可以从 panicking 重新获得控制权，停止终止过程进而恢复正常执行。 recover() 只能在 defer 修饰的函数中使用：用于取得异常调用中传递过来的错误值，如果是正常执行，调用 recover() 会返回 nil，且没有其它效果。 总结：异常会导致栈被展开直到 defer 修饰的 recover() 被调用或者程序中止。
```
func protect(g func()) {
    defer func() {
        log.Println("done")
        // 即使有panic，Println也正常执行。
        if err := recover(); err != nil {
        	log.Printf("run time panic: %v", err)
        }
    }()
    log.Println("start")
    g() //   可能发生运行时错误的地方
}
```
### defer使用的三个规则
规则一 当defer被声明时，其参数就会被实时解析
规则二 defer执行顺序为先进后出
规则三 defer可以读取有名返回值，也就是可以改变有名返回参数的值。

### defer常常可以用来进行函数执行时间的计算
```
package main
import(
        "fmt"
        "time"
)

func main(){
        defer timeCost(time.Now())
        fmt.Println("start program")
        time.Sleep(5*time.Second)
        fmt.Println("finish program")
}

func timeCost(start time.Time){
        terminal:=time.Since(start)
        fmt.Println(terminal)
}
```

### 另外一个计算函数执行时间的方法
利用time包的 Now（）和sub函数
now()返回当前时间
sub（）进行时间的减法运算
```
start := time.Now()
longCalculation()
end := time.Now()
delta := end.Sub(start)
fmt.Printf("longCalculation took this amount of time: %s\n", delta)
```
# 函数
Go语言函数基本组成：关键字func、函数名、参数列表、返回值、函数体和返回语句。

func 函数名（参数列表）（返回值列表）{
	return
}
除了 main（）和init（）函数之外，其他所有类型的函数都可以有参数和返回值

func 为定义函数的关键字

函数签名由函数参数 返回值 以及他们的类型组成
（a typea，b  typeb）（t1 type1，t2 type2）

### 一个函数可以有多个返回值，返回类型之间用逗号隔开并用小括号括上整体

函数体中，参数是局部变量，被初始化为调用者传入的值。函数的参数和具名返回值是函数最外层的局部变量，它们的作用域就是整个函数。如果函数的签名声明了返回值，则函数体的语句列表必须以终止语句结束
```
func IndexRune(s string, r rune) int {//int 就是返回值的类型
	for i, c := range s {
		if c == r {
			return i
		}
	}
	return // 必须要有终止语句，如果这里没有return，则会编译错误：missing return at end of function
}
```
**在 Go 语言里面函数重载是不被允许的**
函数也可以作为函数类型被使用。函数类型也就是函数签名，函数类型表示具有相同参数和结果类型的所有函数的集合。函数类型的未初始化变量的值为nil
例
```
type  funcType func (int, int) int

```
### 函数可以给表达式赋值
函数值字面量，值被称为匿名函数
```
f := func() int{return 7}
```
函数类型的定义和调用
```
package main

import (
	"fmt"
	"time"
)

type funcType func(time.Time)     // 定义函数类型funcType

func main() {
	f := func(t time.Time) time.Time { return t } // 方式一：直接赋值给变量
	fmt.Println(f(time.Now()))

	var timer funcType = CurrentTime // 方式二：定义函数类型funcType变量timer
	timer(time.Now())

	funcType(CurrentTime)(time.Now())  // 先把CurrentTime函数转为funcType类型，然后传入参数调用
// 这种处理方式在Go 中比较常见
}

func CurrentTime(start time.Time) {
	fmt.Println(start)
}

```
## 函数调用
Go 语言中函数默认使用按值传递来传递参数，也就是传递参数的副本。函数接收参数副本之后，在使用变量的过程中可能对副本的值进行更改，但不会影响到原来的变量

如果我们希望函数可以直接修改参数的值，而不是对参数的副本进行操作，则需要将参数的地址传递给函数，这就是按引用传递，比如 Function(&arg1)，此时传递给函数的是一个指针。如果传递给函数的是一个指针，我们可以通过这个指针来修改对应地址上的变量值。

上述调用方式与C++类似


函数调用时，像切片（slice）、字典（map）、接口（interface）、通道（channel）等这样的引用类型都是默认使用引用传递。

命名返回值被初始化为相应类型的零值，当需要返回的时候，我们只需要一条简单的不带参数的return语句。需要注意的是，即使只有一个命名返回值，也需要使用 () 括起来


函数签名中的最后传入参数可以具有前缀为....的类型（...int），这样的函数称为变参函数
## 变参函数
变参函数可以接受某种类型的切片slice为参数
```
package main

import (
	"fmt"
)

// 变参函数，参数不定长
func list(nums ...int) {
	fmt.Println(nums)
}

func main() {
	// 常规调用，参数可以多个
	list(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)

	// 在参数同类型时，可以组成slice使用 parms... 进行参数传递
	numbers := []int{1, 2, 3, 4, 5, 6, 7, 8, 9, 10}
	list(numbers...) // slice时使用
}

```

## 内置函数

内置函数没有标准的类型，因此它们只能出现在调用表达式中，它们不能用作函数值。它们有时可以针对不同的类型进行操作

close（）

用于通道，对于通道c，内置函数close(c)将不再在通道c上发送值。 如果c是仅接收通道，则会出错。 发送或关闭已关闭的通道会导致运行时错误。 关闭nil通道也会导致运行时错误。
new（）和make（）

new 和 make 均是用于分配内存：new用于值类型的内存分配，并且置为零值。make只用于slice、map以及channel这三种引用数据类型的内存分配和初始化。new(T) 分配类型 T 的零值并返回其地址，也就是指向类型 T 的指针。make(T) 它返回类型T的值（不是* T）。

new（s） 为s类型的变量分配内存并进行初始化，返回包含该位置地址的类型*s 的值
len（）计算长度，string（按照字节进行计量） 数组长度，切片长度，字典长度，通道缓冲区排队的元素数
cap（） 数组长度，切片容量，通道缓冲区数量‘

0<=len(s)<=cap(s)

append()   用于附加连接切片
copy（） 用于复制切片
delete 从字典中删除元素


append内置函数是变参函数，常常用来附加切片元素，将零或多个值x附加到S类型的切片s，它的可变参数必须是切片类型，并返回结果切片，也就是是S类型。

copy内置函数常常将切片元素从源src复制到目标dst，并返回复制的元素数。 两个参数必须具有相同的元素类型T，并且必须可以分配给类型为[] T的切片。 复制的元素数量是len（src）和len（dst）的最小值。

内置函数delete从字典m中删除带有键k的元素

delete(m, k)  //从字典m中删除元素 m[k] 

### 复数相关
complex 从浮点实部和虚部构造负数值

real 提取复数的实部

imag 提取复数的虚部
```
complex(realPart, imaginaryPart floatT) complexT
real(complexT) floatT
imag(complexT) floatT
```
### 函数的递归与回调
使用递归函数时经常会遇到的一个重要问题就是栈溢出：一般出现在大量的递归调用导致的内存分配耗尽。有时我们可以通过循环来解决
```
package main

import "fmt"

// Factorial函数递归调用
func Factorial(n uint64)(result uint64) {
    if (n > 0) {
        result = n * Factorial(n-1)
        return result
    }
    return 1
}

// Fac2函数循环计算
func Fac2(n uint64) (result uint64) {
	result = 1
	var un uint64 = 1
	for i := un; i <= n; i++ {
		result *= i
	}
	return
}

func main() {  
    var i uint64= 7
    fmt.Printf("%d 的阶乘是 %d\n", i, Factorial(i)) 
    fmt.Printf("%d 的阶乘是 %d\n", i, Fac2(i))
}

```
Go 语言中也可以使用相互调用的递归函数：多个函数之间相互调用形成闭环。因为 Go 语言编译器的特殊性，这些函数的声明顺序可以是任意的。


Go语言中函数可以作为其它函数的参数进行传递，然后在其它函数内调用执行，一般称之为回调。

```
package main
import(
	"fmt"
)
func main(){
	callback(1,Add)
}

func Add(a,b int){
	fmt.Printf("%d + %d = %d \n",a,b,a+b)

}

func callback(y int ,f func(int,int)){
	f(y,2)
}
```
### 匿名函数
函数值字面量是一种表达式，它的值被称为匿名函数。从形式上看当我们不给函数起名字的时候，可以使用匿名函数

例
```
func(x, y int) int { return x + y }
```
这样的函数不能够独立存在，但可以被赋值于某个变量，即保存函数的地址到变量中：

fplus := func(x, y int) int { return x + y }
然后通过变量名对函数进行调用：

fplus(3, 4)

当然，也可以直接对匿名函数进行调用，注意匿名函数的最后面加上了括号并填入了参数值，如果没有参数，也需要加上空括号，代表直接调用：

func(x, y int) int { return x + y } (3, 4)
下面是一个计算从 1 到 1 百万整数的总和的匿名函数：

func() {
    sum := 0
    for i := 1; i <= 1e6; i++ {
        sum += i
    }
}()

### 闭包函数

闭包是由函数及其相关引用环境组合而成的实体(即：闭包=函数+引用环境)。闭包在运行时可以有多个实例，不同的引用环境和相同的函数组合可以产生不同的实例。由闭包的实质含义，我们可以推论：闭包获取捕获变量相当于引用传递，而非值传递；对于闭包函数捕获的常量和变量，无论闭包何时何处被调用，闭包都可以使用这些常量和变量，而不用关心它们表面上的作用域。

换句话说闭包函数可以访问不是它自己内部的变量（这个变量在其它作用域内声明），且这个变量是未赋值的，它在闭包里面赋值。

我们通过下面代码来看看闭包的使用：
```
package main

import "fmt"

var G int = 7

func main() {
	// 影响全局变量G，代码块状态持续
	y := func() int {
		fmt.Printf("G: %d, G的地址:%p\n", G, &G)
		G += 1
		return G
	}
	fmt.Println(y(), y)
	fmt.Println(y(), y)
	fmt.Println(y(), y) //y的地址

	// 影响全局变量G，注意z的匿名函数是直接执行，所以结果不变
	z := func() int {
		G += 1
		return G
	}()
	fmt.Println(z, &z)
	fmt.Println(z, &z)
	fmt.Println(z, &z)

	// 影响外层（自由）变量i，代码块状态持续
	var f = N()
	fmt.Println(f(1), &f)
	fmt.Println(f(1), &f)
	fmt.Println(f(1), &f)

	var f1 = N()
	fmt.Println(f1(1), &f1)

}

func N() func(int) int {
	var i int
	return func(d int) int {
		fmt.Printf("i: %d, i的地址:%p\n", i, &i)
		i += d
		return i
	}
}


程序输出：
G: 7, G的地址:0x54b1e8
8 0x490340
G: 8, G的地址:0x54b1e8
9 0x490340
G: 9, G的地址:0x54b1e8
10 0x490340
11 0xc0000500c8
11 0xc0000500c8
11 0xc0000500c8
i: 0, i的地址:0xc0000500e8
1 0xc000078020
i: 1, i的地址:0xc0000500e8
2 0xc000078020
i: 2, i的地址:0xc0000500e8
3 0xc000078020
i: 0, i的地址:0xc000050118
1 0xc000078028
```
## 变参函数
支持可变参数列表的函数可以支持任意个传入参数

```
package main

import "fmt"

func Greeting(who ...string) {
	for k, v := range who {

		fmt.Println(k, v)
	}
}

func main() {
	s := []string{"James", "Jasmine"}
	Greeting(s...)  // 注意这里切片s... ，把切片打散传入，与s具有相同底层数组的值。
}

程序输出：
0 James
1 Jasmine
```
