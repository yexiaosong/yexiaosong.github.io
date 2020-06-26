---
title: python核心知识点1
date: 2017-09-14 21:41:27
tags: python
---

## 实现深拷贝
```python
import copy
a = [1,2,3]
b = copy.deepcopy(a)
```
## 私有化
```python
1. _num: 单个下划线，私有化属性或方法，类对象子类可以访问到。无法通过from ** import * 引入
2. __num: 双下划线，避免与子类中的属性名冲突，无法在外界直接访问。
3. __init__ 特殊意义
4. num_ 避免与python 关键字冲突
```
## property的使用
可以类比javascript中的Object.defineProperty
```python
class Num(object):
    def __init__(self):
        self.__num = 100
    def setNum(self, newNum):
        if not isinstance(value, int):
            raise ValueError('please input a number')
        self.__num = newNum
    def getNum(self):
        return self.__num
    num = property(getNum, setNum)

t = Num()
t.num(200)---->把num设置为了200
```
## @property的使用
@装饰器(前言)
```python
class A:
    @staticmethod
    def m(self):
        pass
等同于
class A:
    def m(self):
        pass
    m = staticmethod(m)
```
```python
class Num(object):
    def __init__(self):
        self.__num = 100
    @property # 相当于getNum
    def num(self):
        return self.__num
    @num.setter # 相当于setNum
    def num(self, value):
        if isinstance(value, int):
            self.__num = value
        else:
            raise ValueError('please input a number')
```

## 迭代器

### 判断是否可迭代
```python
from collections import Iterator
isinstance(对象, Iterator)
# 返回True或False
```
### 迭代器转换
```python
其他数据转换为迭代器
arr = [1,2,3]
iter(arr)
# --->输出 <listiterator at 0x*****>
#如果将iter(arr)赋给iterArr,则通过iterArr.next()可以依次取出值，直到报错。
```