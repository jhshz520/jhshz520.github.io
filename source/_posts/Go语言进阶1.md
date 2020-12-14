---
title: Go语言进阶1
date: 2019-11-22 16:15:42
categories: Docker
tags: Go
---
# Go语言进阶

主要是不同于其他语言的部分以及Go语言开发中的应用
# 协程（goroutine）
Go 语言在语言层面上支持了并发，goroutine是Go语言提供的一种用户态线程，有时我们也称之为协程。所谓的协程，某种程度上也可以叫做轻量线程，它不由系统而由应用程序创建和管理，因此使用开销较低（一般为4K）。我们可以创建很多的协程，并且它们跑在同一个内核线程之上的时候，就需要一个调度器来维护这些协程，确保所有的协程都能使用CPU，并且是尽可能公平地使用CPU资源。

调度器的主要有4个重要部分，分别是M、G、P、Sched。

M (work thread) 代表了系统线程内核线程，由操作系统管理。

P (processor) 衔接M和G的调度上下文，它负责将等待执行的G与M对接。P的数量可以通过GOMAXPROCS()来设置，它其实也就代表了真正的并发度，即有多少个goroutine可以同时运行。

G (goroutine) 协程的实体，包括了调用栈，重要的调度信息，例如channel等

N:1 多个（N）用户线程始终在一个内核线程上跑，context上下文切换很快，但是无法真正的利用多核。 1:1 一个用户线程就只在一个内核线程上跑，这时可以利用多核，但是上下文切换很慢，切换效率很低。 M:N 多个协程在多个内核线程上跑，这个可以集齐上面两者的优势，但是无疑增加了调度的难度。

M:N 综合两种方式（N:1，1:1）的优势。多个协程可以在多个内核线程上处理。既能快速切换上下文，也能利用多核的优势，而Go正是选择这种实现方式。

Go 语言中的协程是运行在多核CPU中的(通过runtime.GOMAXPROCS(1)设定CPU核数)。 实际中运行的CPU核数未必会和实际物理CPU数相吻合。

每个协程都会被一个特定的P(某个CPU)选定维护，而M(物理计算资源)每次挑选一个有效P，然后执行P中的协程。

每个P会将自己所维护的协程放到一个G队列中，其中就包括了协程堆栈信息，是否可执行信息等等。

默认情况下，P的数量与实际物理CPU的数量相等。当我们通过循环来创建协程时，协程会被分配到不同的G队列中。 而M的数量又不是唯一的，当M随机挑选P时，也就等同随机挑选了协程。

协程进入P管理的队列G是带有随机性的。

P的数量由runtime.GOMAXPROCS(1)所设定，通常来说它是和内核数对应，例如在4Core的服务器上会启动4个线程。G会有很多个，每个P会将协程从一个就绪的队列中做Pop操作，为了减小锁的竞争，通常情况下每个P会负责一个队列

```
runtime.NumCPU()        // 返回当前CPU内核数
runtime.GOMAXPROCS(2)  // 设置运行时最大可执行CPU数
runtime.NumGoroutine() // 当前正在运行的协程 数
```
P维护着这个队列（称之为runqueue），Go语言里，启动一个协程很容易：go function 就行，所以每有一个go语句被执行，runqueue队列就在其末尾加入一个协程，在下一个调度点，就从runqueue中取出一个协程执行


假如有两个M，即两个内核线程，分别对应一个P，每一个P调度一个G队列。如此一来，就组成的协程运行时的基本结构：

当有一个M返回时，它必须尝试取得一个P来运行协程，一般情况下，它会从其他的OS Thread线程那里窃取一个P过来，如果没有拿到，它就把协程放在一个global runqueue里，然后自己进入线程缓存里。

如果某个P所分配的任务G很快就执行完了，这会导致多个队列存在不平衡，会从其他队列中截取一部分协程到P上进行调度。一般来说，如果P从其他的P那里要取任务的话，一般就取run queue的一半，这就确保了每个内核线程都能充分的使用。

当一个内核线程被阻塞时，P可以转而投奔另一个内核线程

运行下面代码体验下Go语言中通过设定runtime.GOMAXPROCS(2) ，也即手动指定CPU运行的核数

**注意 递归函数的计算很耗费cpu和内存，运行时可以根据电脑配置修改循环和递归的数量**

```
package main

import (
	"fmt"
	"runtime"
	"sync"
	"time"
)

var quit chan int = make(chan int)

func loop() {
	for i := 0; i < 1000; i++ {
		Factorial(uint64(1000))
	}
	quit <- 1
}
func Factorial(n uint64) (result uint64) {
	if n > 0 {
		result = n * Factorial(n-1)
		return result
	}
	return 1
}

var wg1, wg2 sync.WaitGroup

func main() {
	fmt.Println("1:", time.Now())
	fmt.Println(runtime.NumCPU()) // 默认CPU核数
	a := 5000
	for i := 1; i <= a; i++ {
		wg1.Add(1)
		go loop()
	}

	for i := 0; i < a; i++ {
		select {
		case <-quit:
			wg1.Done()
		}
	}
	fmt.Println("2:", time.Now())
	wg1.Wait()

	fmt.Println("3:", time.Now())
	runtime.GOMAXPROCS(2) // 设置执行使用的核数
	a = 5000
	for i := 1; i <= a; i++ {
		wg2.Add(1)
		go loop()
	}

	for i := 0; i < a; i++ {
		select {
		case <-quit:
			wg2.Done()
		}
	}

	fmt.Println("4:", time.Now())
	wg2.Wait()
	fmt.Println("5:", time.Now())
}
```
# goroutine
在Go语言中，协程的使用很简单，直接在函数（代码块）前加上关键字 go 即可。go关键字就是用来创建一个协程的，后面的代码块就是这个协程需要执行的代码逻辑
```
package main

import (
	"fmt"
	"time"
)

func main() {
	for i := 1; i < 10; i++ {
		go func(i int) {
			fmt.Println(i)
		}(i)
	}
	// 暂停一会，保证打印全部结束
	time.Sleep(1e9)
}
```
time.Sleep(1e9)让主程序不会马上退出，以便让协程运行完成，避免主程序退出时协程未处理完成甚至没有开始运行。

# 通道（channel）

Go 奉行通过通信来共享内存，而不是共享内存来通信。所以，channel 是协程之间互相通信的通道，协程之间可以通过它发送消息和接收消息

通道消息传递与消息类型也有关系，一个通道只能传递（发送send或接收receive）类型的值，这需要在声明通道时指定。

默认情况下，通道是阻塞的 (叫做无缓冲的通道)

### 通道的建立
使用make来建立一个通道：
```
var channel chan int = make(chan int)
// 或
channel := make(chan int)
```
Go中通道可以是发送（send）、接收（receive）、同时发送（send）和接收（receive）。
```
// 定义接收的通道
receive_only := make (<-chan int)
 
// 定义发送的通道
send_only := make (chan<- int)

// 可同时发送接收
send_receive := make (chan int)
```
chan <-数据进入通道，对于调用者就是发送
<-chan,表示数据从通道中出来，对于调用者来说就是接受数据

定义只发送或者只接受的通道意义不大，这样的通道一般只用在参数的传递中
```
package main

import (
	"fmt"
	"time"
)

func main() {
	c := make(chan int) // 不使用带缓冲区的通道
	go send(c)
	go recv(c)
	time.Sleep(3 * time.Second)
close(c)
}

// 只能向chan里send数据
func send(c chan<- int) {
	for i := 0; i < 10; i++ {

		fmt.Println("send readey ", i)
		c <- i
		fmt.Println("send ", i)
	}
}

// 只能接收通道中的数据
func recv(c <-chan int) {
	for i := range c {
		fmt.Println("received ", i)
	}
}
```
运行结果上我们可以发现一个现象，往通道发送数据后，这个数据如果没有被取走，通道是阻塞的，也就是不能继续向通道里面发送数据。

如果没有指定通道缓冲区的大小，默认情况下通道是阻塞的

我们可以建立带缓冲区的通道
```
c := make(chan int, 1024)
```
带有缓冲区的通道，在缓冲区有数据而未填满前，读取不会出现阻塞的情况

无缓冲区的通道，只有发送协程和接收协程同时准备好才能完成发送和接收的操作，否则通道处于阻塞状态
但是唯一的好处时这些能保证发送和接收是同步的


无缓冲的通道保证进行发送和接收的协程会在同一时间进行数据交换；有缓冲的通道没有这种保证

### 通道的关闭

close（）可以关闭通道

通道不需要经常关闭

关闭通道后，无法向通道再发送数据

关闭通道后可以继续向通道接收数据不能继续发送数据（如果继续发送数据将会导致panic错误，导致接收后立即返回零值）

对于nil通道，无论收发都会阻塞

## 锁与同步
### 同步锁
Go语言包中的sync包提供了两种锁类型：sync.Mutex和sync.RWMutex，前者是互斥锁，后者是读写锁。

互斥锁是传统的并发程序对共享资源进行访问控制的主要手段，在Go中，似乎更推崇由channel来实现资源共享和通信。它由标准库代码包sync中的Mutex结构体类型代表。只有两个公开方法：调用Lock（）获得锁，调用unlock（）释放锁。

使用Lock()加锁后，不能再继续对其加锁（同一个goroutine中，即：同步调用），否则会panic。只有在unlock()之后才能再次Lock()。异步调用Lock()，是正当的锁竞争，当然不会有panic了。适用于读写不确定场景，即读写次数没有明显的区别，并且只允许只有一个读或者写的场景，所以该锁也叫做全局锁。


func (m *Mutex) Unlock()用于解锁m，如果在使用Unlock()前未加锁，就会引起一个运行错误。已经锁定的Mutex并不与特定的goroutine相关联，这样可以利用一个goroutine对其加锁，再利用其他goroutine对其解锁

### 互斥锁的经典模式

```
var lck sync.Mutex
func foo() {
    lck.Lock() 
    defer lck.Unlock()
    // ...
}
```

lck.Lock()会阻塞直到获取锁，然后利用defer语句在函数返回时自动释放锁

实例如下：
```
package main

import (
	"fmt"
	"sync"
	"time"
)

func main() {
	wg := sync.WaitGroup{}

	var mutex sync.Mutex
	fmt.Println("Locking  (G0)")
	mutex.Lock()
	fmt.Println("locked (G0)")
	wg.Add(3)

	for i := 1; i < 4; i++ {
		go func(i int) {
			fmt.Printf("Locking (G%d)\n", i)
			mutex.Lock()
			fmt.Printf("locked (G%d)\n", i)

			time.Sleep(time.Second * 2)
			mutex.Unlock()
			fmt.Printf("unlocked (G%d)\n", i)
			wg.Done()
		}(i)
	}

	time.Sleep(time.Second * 5)
	fmt.Println("ready unlock (G0)")
	mutex.Unlock()
	fmt.Println("unlocked (G0)")

	wg.Wait()
}
```

有锁释放时才能进行lock动作，G0锁释放后才能后续锁释放的可能

Mutex也可以作为struct的一部分，这样这个struct就会防止多线程更改数据
```
package main

import (
	"fmt"
	"sync"
	"time"
)

type Book struct {
	BookName string
	L        *sync.Mutex
}

func (bk *Book) SetName(wg *sync.WaitGroup, name string) {
	defer func() {
		fmt.Println("Unlock set name:", name)
		bk.L.Unlock()
		wg.Done()
	}()

	bk.L.Lock()
	fmt.Println("Lock set name:", name)
	time.Sleep(1 * time.Second)
	bk.BookName = name
}

func main() {
	bk := Book{}
	bk.L = new(sync.Mutex)
	wg := &sync.WaitGroup{}
	books := []string{"《三国演义》", "《道德经》", "《西游记》"}
	for _, book := range books {
		wg.Add(1)
		go bk.SetName(wg, book)
	}

	wg.Wait()
}
程序输出：

Lock set name: 《西游记》
Unlock set name: 《西游记》
Lock set name: 《三国演义》
Unlock set name: 《三国演义》
Lock set name: 《道德经》
Unlock set name: 《道德经》

```

### 读写锁
读写锁由结构体类型sync.RWMutex代表

1. 读锁与写锁之间是互斥
2. 读锁定情况下，对读写锁进行写锁定，将阻塞；加读锁时不会阻塞
3. 对未被加锁的结构进行写解锁，读解锁 会引发panic
4. 写解锁 进行的同时会试图唤醒所有因进行读锁定而被阻塞的进程
5.  读解锁进行的同时会唤醒一个 因写锁定而被阻塞的进程
### RWMutex 提供四个方法
```
func (*RWMutex) Lock // 写锁定
func (*RWMutex) Unlock // 写解锁

func (*RWMutex) RLock // 读锁定
func (*RWMutex) RUnlock // 读解锁
```
```
package main

import (
	"fmt"
	"sync"
	"time"
)

var m *sync.RWMutex

func main() {
	wg := sync.WaitGroup{}
	wg.Add(20)
	var rwMutex sync.RWMutex
	Data := 0
	for i := 0; i < 10; i++ {
		go func(t int) {
			rwMutex.RLock()
			defer rwMutex.RUnlock()
			fmt.Printf("Read data: %v\n", Data)
			wg.Done()
			time.Sleep(2 * time.Second)
			// 这句代码第一次运行后，读解锁。
			// 循环到第二个时，读锁定后，这个协程就没有阻塞，同时读成功。
		}(i)

		go func(t int) {
			rwMutex.Lock()
			defer rwMutex.Unlock()
			Data += t
			fmt.Printf("Write Data: %v %d \n", Data, t)
			wg.Done() 

			// 这句代码让写锁的效果显示出来，写锁定下是需要解锁后才能写的。
			time.Sleep(2 * time.Second)		
		}(i)
	}
	time.Sleep(5 * time.Second)
	wg.Wait()
}

```

### sync.WaitGroup

等待一组线程集合完成，才会继续向下执行，主线程（Goroutine）调用Add（）来设置等待的线程的数量，每个线程运行，完成后调用Done（）

Wait（）用来阻塞，直到所有线程完成后才会向执行。 Add（-1）和Done（）效果一致
```
var wg sync.WaitGroup
	for i := 0; i < 10; i++ {
		wg.Add(1)
		go func(t int) {
			defer wg.Done()
			fmt.Println(t)
		}(i)
	}
	wg.Wait()
```
### sync.Once
sync.Once.Do(f func())能保证once只执行一次,这个sync.Once块只会执行一次

```
for i, v := range make([]string, 10) {
		once.Do(onces)
		fmt.Println("v:", v, "---i:", i)
	}

	for i := 0; i < 10; i++ {

		go func(i int) {
			once.Do(onced)
			fmt.Println(i)
		}(i)
	}
	time.Sleep(4000)
}
func onces() {
	fmt.Println("onces")
}
func onced() {
	fmt.Println("onced")
```

### sync.Map
原生支持并发安全的map

普通map并不是线程安全（或者说并发安全），但一般情况下我们还是使用它，因为这足够了；只有在涉及到线程安全，再考虑sync.map

sync.Map的读取并不是类型安全的，所以我们在使用Load读取数据的时候我们需要做类型转换

### 普通map和sync.Map的差异

较大

```
var m sync.Map

	//Store
	m.Store("name", "Joe")
	m.Store("gender", "Male")

	//LoadOrStore
	//若key不存在，则存入key和value，返回false和输入的value
	v, ok := m.LoadOrStore("name1", "Jim")
	fmt.Println(ok, v) //false Jim

	//若key已存在，则返回true和key对应的value，不会修改原来的value
	v, ok = m.LoadOrStore("name", "aaa")
	fmt.Println(ok, v) //true Joe

	//Load
	v, ok = m.Load("name")
	if ok {
		fmt.Println("key存在，值是： ", v)
	} else {
		fmt.Println("key不存在")
	}

	//Range
	//遍历sync.Map
	f := func(k, v interface{}) bool {
		fmt.Println(k, v)
		return true
	}
	m.Range(f)

	//Delete
	m.Delete("name1")
	fmt.Println(m.Load("name1"))
```