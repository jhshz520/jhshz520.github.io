---
title: Go语言进阶3
date: 2019-11-27 10:20:13
categories: Docker
tags: Go
---
# sort包
sort包实现了三种基本的排序方法，插入，快排和堆排
使用sort包时无需考虑具体细节

## sort.Interface接口定义了三个方法
### Len（）
集合长度
### Less（）
比较
### Swap（）
交换位置

## sort包原生支持[]int,[]float64,[]string三种内建数据类型的切片排序操作

## 默认升序，
简单实现降序排列的例子：
```
package main

import (
	"fmt"
	"sort"
)

func main() {
	a := []int{4, 3, 2, 1, 5, 9, 8, 7, 6}
	sort.Sort(sort.Reverse(sort.IntSlice(a)))
	fmt.Println("After reversed: ", a)
}

```

## 自定义sort.Interface排序
如果是具体的某个结构体的排序，就需要自己实现Interface了

排序需要实现sort.Interface接口的三个方法，即：Len()，Swap(i, j int)，Less(i, j int)

下面是一个自行实现sort的例子
```
package main

import (
	"fmt"
	"sort"
)

type person struct {
	Name string
	Age  int
}

type personSlice []person

func (s personSlice) Len() int           { return len(s) }
func (s personSlice) Swap(i, j int)      { s[i], s[j] = s[j], s[i] }
func (s personSlice) Less(i, j int) bool { return s[i].Age < s[j].Age }

func main() {
	a := personSlice{
		{
			Name: "AAA", 
			Age:  55, 
		}, 
		{
			Name: "BBB", 
			Age:  22, 
		}, 
		{
			Name: "CCC", 
			Age:  0, 
		}, 
		{
			Name: "DDD", 
			Age:  22, 
		}, 
		{
			Name: "EEE", 
			Age:  11, 
		}, 
	}
	sort.Sort(a)
	fmt.Println("Sort:", a)

	sort.Stable(a)
	fmt.Println("Stable:", a)

}
```

## sort.Slice
使用了反射，一般不建议使用
