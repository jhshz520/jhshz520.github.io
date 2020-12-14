---
title: python学习1
date: 2020-12-09 16:29:51
categories: python
tags: python
---
# python 面向对象

```
class Employee:
   '所有员工的基类'
   empCount = 0
 
   def __init__(self, name, salary):
      self.name = name
      self.salary = salary
      Employee.empCount += 1
   
   def displayCount(self):
     print "Total Employee %d" % Employee.empCount
 
   def displayEmployee(self):
      print "Name : ", self.name,  ", Salary: ", self.salary
```
empCount 变量是一个类变量，它的值将在这个类的所有实例之间共享。你可以在内部类或外部类使用 Employee.empCount 访问。

第一种方法__init__()方法是一种特殊的方法，被称为类的构造函数或初始化方法，当创建了这个类的实例时就会调用该方法

self 代表类的实例，self 在定义类的方法时是必须有的，虽然在调用时不必传入相应的参数

**self代表类的实例，而非类**

```
class Test:
    def prt(self):
        print(self)
        print(self.__class__)
 
t = Test()
t.prt()
```
类的方法与普通的函数只有一个特别的区别——它们必须有一个额外的第一个参数名称, 按照惯例它的名称是 self

打印结果

<__main__.Test instance at 0x10d066878>
__main__.Test

## 创建实例对象
实例化类其他编程语言中一般用关键字 new，但是在 Python 中并没有这个关键字，类的实例化类似函数调用方式。

以下使用类的名称 Employee 来实例化，并通过 __init__ 方法接收参数。

```
"创建 Employee 类的第一个对象"
emp1 = Employee("Zara", 2000)
"创建 Employee 类的第二个对象"
emp2 = Employee("Manni", 5000)
```
## 访问属性
emp1.displayEmployee()
emp2.displayEmployee()
print "Total Employee %d" % Employee.empCount

## 完整实例

```
#!/usr/bin/python
# -*- coding: UTF-8 -*-
 
class Employee:
   '所有员工的基类'
   empCount = 0
 
   def __init__(self, name, salary):
      self.name = name
      self.salary = salary
      Employee.empCount += 1
   
   def displayCount(self):
     print "Total Employee %d" % Employee.empCount
 
   def displayEmployee(self):
      print "Name : ", self.name,  ", Salary: ", self.salary
 
"创建 Employee 类的第一个对象"
emp1 = Employee("Zara", 2000)
"创建 Employee 类的第二个对象"
emp2 = Employee("Manni", 5000)
emp1.displayEmployee()
emp2.displayEmployee()
print "Total Employee %d" % Employee.empCount
```

你也可以使用以下函数的方式来访问属性：

getattr(obj, name[, default]) : 访问对象的属性。
hasattr(obj,name) : 检查是否存在一个属性。
setattr(obj,name,value) : 设置一个属性。如果属性不存在，会创建一个新属性。
delattr(obj, name) : 删除属性。

## python 内置类属性

```
__dict__ : 类的属性（包含一个字典，由类的数据属性组成）

__doc__ :类的文档字符串


__name__: 类名

__module__: 类定义所在的模块（类的全名是'__main__.className'，如果类位于一个导入模块mymod中，那么className.__module__ 等于 mymod）

__bases__ : 类的所有父类构成元素（包含了一个由所有父类组成的元组）
```
**调用实例**

```
#!/usr/bin/python
# -*- coding: UTF-8 -*-
 
class Employee:
   '所有员工的基类'
   empCount = 0
 
   def __init__(self, name, salary):
      self.name = name
      self.salary = salary
      Employee.empCount += 1
   
   def displayCount(self):
     print "Total Employee %d" % Employee.empCount
 
   def displayEmployee(self):
      print "Name : ", self.name,  ", Salary: ", self.salary
 
print "Employee.__doc__:", Employee.__doc__
print "Employee.__name__:", Employee.__name__
print "Employee.__module__:", Employee.__module__
print "Employee.__bases__:", Employee.__bases__
print "Employee.__dict__:", Employee.__dict__



输出结果：
Employee.__doc__: 所有员工的基类
Employee.__name__: Employee
Employee.__module__: __main__
Employee.__bases__: ()
Employee.__dict__: {'__module__': '__main__', 'displayCount': <function displayCount at 0x10a939c80>, 'empCount': 0, 'displayEmployee': <function displayEmployee at 0x10a93caa0>, '__doc__': '\xe6\x89\x80\xe6\x9c\x89\xe5\x91\x98\xe5\xb7\xa5\xe7\x9a\x84\xe5\x9f\xba\xe7\xb1\xbb', '__init__': <function __init__ at 0x10a939578>}
```

## 垃圾回收
python对象销毁(垃圾回收)
Python 使用了引用计数这一简单技术来跟踪和回收垃圾。

在 Python 内部记录着所有使用中的对象各有多少引用。
一个内部跟踪变量，称为一个引用计数器。

当对象被创建时， 就创建了一个引用计数， 当这个对象不再需要时， 也就是说， 这个对象的引用计数变为0 时， 它被垃圾回收。但是回收不是"立即"的， 由解释器在适当的时机，将垃圾对象占用的内存空间回收

```
a = 40      # 创建对象  <40>
b = a       # 增加引用， <40> 的计数
c = [b]     # 增加引用.  <40> 的计数

del a       # 减少引用 <40> 的计数
b = 100     # 减少引用 <40> 的计数
c[0] = -1   # 减少引用 <40> 的计数
```
垃圾回收机制不仅针对引用计数为0的对象，同样也可以处理循环引用的情况。循环引用指的是，两个对象相互引用，但是没有其他变量引用他们。这种情况下，仅使用引用计数是不够的。Python 的垃圾收集器实际上是一个引用计数器和一个循环垃圾收集器。作为引用计数的补充， 垃圾收集器也会留心被分配的总量很大（即未通过引用计数销毁的那些）的对象。 在这种情况下， 解释器会暂停下来， 试图清理所有未引用的循环

### 实例

```
析构函数 __del__ ，__del__在对象销毁的时候被调用，当对象不再被使用时，__del__方法运行

#!/usr/bin/python
# -*- coding: UTF-8 -*-
 
class Point:
   def __init__( self, x=0, y=0):
      self.x = x
      self.y = y
   def __del__(self):
      class_name = self.__class__.__name__
      print class_name, "销毁"
 
pt1 = Point()
pt2 = pt1
pt3 = pt1
print id(pt1), id(pt2), id(pt3) # 打印对象的id
del pt1
del pt2
del pt3
```
运行结果：

```
3083401324 3083401324 3083401324
Point 销毁
```

<font color="yellow">
单下划线、双下划线、头尾双下划线说明：
__foo__: 定义的是特殊方法，一般是系统定义名字 ，类似 __init__() 之类的。

_foo: 以单下划线开头的表示的是 protected 类型的变量，即保护类型只能允许其本身与子类进行访问，不能用于 from module import *

__foo: 双下划线的表示的是私有类型(private)的变量, 只能是允许这个类本身进行访问了
</font>