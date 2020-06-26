---
title: python核心知识点2
date: 2017-09-17 00:32:23
tags: python
---

## python作用域
### global
可以在定义变量的前面加上global，这样可以覆盖掉全局变量
### locals()
打印局部变量
```python
def fun():
    age = 22
    name = 'Jack'
    print(locals())
```
## types.MethodType
可以实现动态添加方法
```python
import types
def get_grade(self):
    if self.score >=80:
        return 'A'
    return 'B'

class Stu(object):
    def __init__(self, name, score)
        self.name = name
        self.score = score

s1 = Stu('Alex',100)
s1.get_grade = types.MethodType(get_grade, s1, Stu)
print s1.get_score()

如果不采用这种方式，直接调用get_score方法，则会报出Attribute的错误
```
## 创建类
type关键词一方面可以用来显示属性，另一方面可以动态创建属性。
实质上，type是在python中创建类的最初元类，可以使用__class__属性查看父类，类似于js的__proto__，在各种类的尽头都是type
### 使用type动态创建类
```python
class Animal:
    def eat(self):
        print("----eat----")
Dog = type("Dog",(Animal,),{})
其中type后的第一个参数为类名(字符串)，第二个为继承元素(元组)，第三个为类中的属性(字典)。
```
### __metaclass__属性
这个属性是python中定义类的一种拦截方式，因为在python中定义类时，首先会寻找本创建对象中有没有__metaclass__属性，如果有会根据里面的设置创建类。如果没有回去父类中，然后模块中，然后内置的type。初学用不到，后期补充
### __slots__
由于动态语言的动态性能，导致实例的类和属性不可控，所以，可以使用__slot__来限制可以定义的属性，例如：
```python
class Stu(object):
    __slot__ = ('name','age')
s1 = Stu()
s1.name = 'Jack'
s1.age = 22
s1.sex = 'male'----->报错，AttributeError
```
但是此属性只对当前类的实例有作用，对继承的类没作用。
## 垃圾回收
python以引用计数为主，标记清除和分代收集两种机制为辅。
### 小整数池
范围在[-5,256]的数值，在python对象创建时已经创建了。所以在定义一系列值为1时，id都为一致的。
同理有字符串等

> 详细GC机制不在总结，用到时再总结。

## 内建属性及方法
### 常用内建属性
+ __init__ 构造初始化函数，创建实例后，赋值时使用。再__new__之后
+ __new__ 创建实例时调用
+ __str__ 实例字符串表示，突出可读性，如print
+ __repr__ 实例字符串表示，突出准确性
+ __del__ 引用计数为零的时候调用
+ __doc__ 类文档，使用help时调用
+ __class__ 实例所在的类
+ __getattribute__ 属性拦截器，类比于JavaScript中Object.defineProperty中的getter, 再__getattribute__中，不要使用self属性，可能会报错

### 常用内建方法
+ range() 
+ map 函数，类比js中的map
+ filter ，类比js中的filter
+ reduce
+ set