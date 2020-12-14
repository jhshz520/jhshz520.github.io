---
title: map初始化的几种方式
date: 2019-10-12 09:03:19
tags: leetcode
categories: Cpp算法实践
---
# map初始化的方式

## 1 insert方式
```
 map<char,int> m;
        m.insert(make_pair('I',1));
        m.insert(make_pair('V',5));
        m.insert(make_pair('X',10));
        m.insert(make_pair('L',50));
        m.insert(make_pair('C',100));
        m.insert(make_pair('D',500));
        m.insert(make_pair('M',1000));
```
## 2直接赋值方式
```
map<char, int> table = {
            {'I', 1},
            {'V', 5},
            {'X', 10},
            {'L', 50},
            {'C', 100},
            {'D', 500},
            {'M', 1000}
        };
```
### string 字符串类型的每一个s[i]实质上都是一个char类型，因此不用特别的强制类型转换成char