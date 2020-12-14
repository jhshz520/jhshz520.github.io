---
title: java基础学习
date: 2020-01-10 09:42:52
categories: JAVA学习
tags: 编程语言
---
# 常用的类
## Object类
### Object类成员方法
public inthashCode（）


public final Class getClass（）


public String toString（）



public boolean equals（Object obj）


protected void finalize（）

protected Obj clone（）

## Scanner类
构造方法

public Scanner（InputStream source）
```
Scanner sc = new Scanner(System.in);
String s = sc.nextLine();
int s = sc.nextInt();
```

### Scanner类的成员方法
hasNextXxx（）判断是否有下一个输入项可以是Int，Double

如果是字符串，则可以省略Xxx
nextXxx（）获取下一个输入项

默认情况，Scanner使用空格，回车等作为分隔符

### 常用方法
public int nextInt()

public String nextLine()

## String 类
### 构造方法
public String（）
public String(byte[] bytes)
public String（byte[] bytes,int offset,int length）
public String(char[] value)
public String(char[] value,int offset,int length)
public String(String original)
### 查找字符串功能
indexOf（），返回字符串首个匹配位置
### 判断功能
equals(),equalsIgnoreCase(),contains(),startsWith(),endsWith(),isEmpty()
### 获取功能
length（），charAt（），IndexOf（ch），IndexOf（str）,substring（int start），substring（int start，int end）
### 转换功能
getBytes（）,toCharArray(),valueOf(char[] chs),valueOf(int i),toLowerCase(),toUpperCase(),concat(String str)
### 替换功能
replace(char old,char new),repalce(String old,String new)
### 去除字符串两端空格
String trim（）
### 按字典顺序比较两个字符串
int compareTo(String str)
int compareToIgnoreCase(String str)
## StringBuffer类
如果对string进行拼接，每次拼接都会构建新的string对象耗时又浪费空间，用StringBuffer可以比较好的解决这个问题
### 添加功能
append（），insert（int offset，String str）
### 删除功能
deleteCharAt（intindex）

delete（int start，int end）
### 替换功能
replace（int start，int end，String str）
### 反转功能
reverse（）
### 截取功能
substring（int start），substring（int start，int end）
截取功能是返回一个str，但是本身是没有发生变化的

## Arrays类
toString（int[] a）
sort(int[]a)
binarySearch(int[] a,int key)
### 基本类型封装成包装类的好处在于可以在对象中定义更多的方法来操作数据
基本类和包装类的对应关系

Byte，Short，Integer，Long，Float，Double，Character，boolean

## Integer类
构造方法
Integer（int value）
Integer（String s）
### 类成员方法
intValue（），int parseInt（String s）， String toString（int i），Integer valueOf（int i） Integer valueOf（String s）

## Charcter 类
### 构造方法
Character（char value）
### 类成员方法
isUpperCase（）

isLowerCase（）

isDigit（）

toUpperCase（）

toLowerCase（）
## 正则表达式
java.util.regex Pattern
### 操作
1 字符
x  字符x
\\ 反斜线字符
\n 换行符
\r 换行符
2 字符类
[abc] a,b或者c
[^abc] 除a,b,b外的任何字符
[a-zA-Z]a到z或者A到Z,两头的字母包括在内
[0-9] 包括数字字符0-9
3 预定义字符类
. 任何字符
\d 数字：[0-9]
\w 单词字符 [a-zA-Z_0-9]
4 边界匹配器
^ 行的开头
$ 行的结尾
\b 单词边界
5 数量词
X? X一个或者一次也没有
X* X零次或者多次
X+ X一次或者多次
X{n} X恰好n次
X{n,} X至少n次
X{n,m} X至少n次，至多不超多m次
### 判断功能
matches（String regex）
### 分割功能
split（String regex）
### 替换功能
repalceAll(String regex,String replacement)
### 获取功能
Patter和Matcher类
## Math类
### 成员方法
abs（），double ceil（），floor（），max（），pow（double a，double b），random（），int round(float a),double sqrt(double a)

## Random类
### 构造方法
Random（）
Random（long seed）
### 类的成员方法
int nextInt（）int范围内的数据
int nextInt（int n）[0,n]的数据
## System类
包含有用的类字段和方法不能被直接实例化
### 成员方法
void gc（）

void exit（int status）

long currentTimeMillils（）

arraycopy（Object src，int srcPos，Object destPos，int length）将字符串1的起始位置处的字符复制到字符串二的起始位置处，复制的字符串的长度为最后一个参数length
## Array类
### 重要方法
Arrays.sort（）传入一个字符串数组
Arrays.toString()把数组转换成字符串
Arrays.binarySearch(arr,24)二分查找，如果没有找到返回数组长度的负值

### String与int的互转
```
// int -- String
		int number = 100;
		// 方法1
		String s1 = number + "";
		//方式2
		String s2 = String.valueOf(number);
		//方式3
		//int -- Integer -- String
		Integer i = new Integer(number);
		String s3 = i.toString();
		//方式4
		String s4 = Integer.toString(number);
		System.out.println("--------------");

		String s = "100";
		//方式1
		//String -- Integer -- int
		Integer ii = new Integer(s);
		//public int intValue()
		int num = ii.intValue();
		//方式2
		//public static int parseInt(String s)
		int num2 = Integer.parseInt(s);
```
### 自动装箱 int->integer
底层方法Interger valueOf（int i）
### 自动拆箱 Integer->int
底层方法 intinvalue（）
## StringBuffer
长度可以变
### 成员方法
int length() 实际值

int capacity() 理论值

append（）

reverse（）

replace（int start，int end， String str）//用给定字符串替换从指定位置开始到指定位置结束的数据

deleteCharAt（int index）//删除指定位置字符

delete（int start，int end）//删除从指定位置开始到指定位置结束的内容

## string和stringbuffer的互转
String s;
StringBuffer sb = new StringBuffer(s);
sb.reverse();
String ss = new String(sb);

## String是一种特殊的引用类型，在参数传递时可以当做基本类型来看，因为传递的是常量值

## BigInteger

## BigDecimal

能够减少精度的损失
## Date
### 构造函数
默认是当前时间public Date（）
public Date（long date）：把给定的毫秒值赋值给日期对象
### 成员方法
public long getTime() 获取毫秒值
public void setTime（long time）：把毫秒值当做参数传递


 long time = System.currentTimeMillis();//获取当前时间的毫秒级表示

 ## DateFormat 针对日期进行格式化和解析的抽象类
 格式化 日期->文本
public final String format（Date date）

 解析 文本->日期

 public Date parse（String source）

 # Collection
 对象数组的长度是固定的，适应不了变化的需求，用collection
 长度可变
 只能存储对象
 可以存储不同类型的对象

数组和集合的区别？
集合长度可变，只能存储引用数据类型，可以存储不同数据类型的元素

数组长度固定 数组可以存储引用数据类型，也可以存储基本数据类型，数组存储的是同一种数据类型的元素


 ## collection的功能

 A 添加
 boolean add：集合添加一个元素
 boolean addAll(Collection c):集合中添加多个元素
 B 获取
 Iterator iterator（）：迭代器
 int size（） 集合元素个数长度
 C 删除
 void clear（）：清空集合的所有元素
 boolean remove（Object obj）：从集合中移除一个元素
 boolean removeAll（Collection c）：从集合中移除多个元素
 D 判断
 boolean contains（Object obj）判断集合中是否包含指定元素
 boolean containsAll（Collection c ）判断集合中是否包含指定的多个元素
 boolean isEmpty() 判断集合是否为空

 E 交集元素
 boolean retainAll（Collection c）
 F 转数组
 Object[] toArray(): 集合转换为对象数组

