---
title: python学习
date: 2017-09-05 21:30:25
tags: learn
category: 'python'
---
## python 字符格式化
### python采用和c语言的方式，%格式化。例如：
```python
>>> '%2d-%02d' % (3, 1)
' 3-01'  
```
其中，2d%中的2表示在输出后两个空格，02d%表示两个空格，且输出保持为前面补零。

## 代码块和条件判断
 ### :表示后面缩进的代码是一个块
 ### 条件判断
 ```python
 if sth:
 	pass
 elif sth else:
 	pass
 else:
 ```
## 循环
### 迭代式
```python
names = ['Michael', 'Bob', 'Tracy']
for name in names:
    print(name)
```

### 如果使用类似Java中的key-value形式，需要使用python内置函数enumerate，把list变成k-v对，例如
 ```python
 for i,value in enumerate(['A', 'B', 'C']):
 	print(i, value)   #---->0 A      1 B      2 C
 ```
 
### 遍历dict，生成k,v对可以遍历dict的items方法。否则只能遍历key值
 ```python
d = {'one': 'laoda','two':'loaer','three': 'laosan'}
for k,v in d.items():
	print(k, '= ', v)
 ```
 
### 判断组件是否可遍历
 ```python
 from collections import Iterable
 if isinstance('abc',Iterable):
 	print(True)
 ```
 
### 条件循环
```python
a=12
while a > 0:
	a-=5
print(a)    ---->a=-3
```
### range函数
list(range(5)) 生产从0到4的list
使用for item in range(5),则可以遍历出从0到4
## 列表的增删查改---list
### 添加
   + append()
   + insert(1,'item')，在下标为一的位置，增加了一个item元素
   + extent()  ,可以拓充列表，将括号内的列表加到前面的列表中
### 删除元素
   + pop() 删除最后一个
   + remove() 根据内容删除
   + del xxx[下标]根据下标删除
### 查询   判断元素是否在列表中
 	if "xxx" in list  || if "xxx" not in list
### 修改
 xxx[下标]=''
 
## 元组---tuple
 ### 格式
 classmates = ('Michael', 'Bob', 'Tracy')
 ### 特点： 
 只读
 ### 坑：
  t=(1) ,表示t=1，与数学中的小括号冲突，此时使用t=(1,) ,表明t是一个只有1的元组
## 字典的增删改查---dict
### 增加dic={'name':'alex'}  dic['age']=22
### 查： 使用get()，可以避免错误
 
## python切片
 字符串，列表，元组等中，使用。
### 基础用法
```python
L = [1,2,3,4,5]
L[1:2] 则表示从下标为1到下表为2，包前不顾后。正数0为第一个，倒数第一个下标为-1.

```
### 还可以选择步长，例如
```python
L2 = range(10)
L2[2:8:3] ------> [2,5]
```
## 列表生成式
### 格式
```python
[ x*x for x in range(1,11)]
则生成一个[1,4,9...100]的列表
```

### 后面还可以写判断，例如
```python
[x * x for x in range(1, 11) if x % 2 == 0]
则生成一个[4，16，36，64，100]的序列
```

+ 将list中的大写转为小写
[s.lower() for s in L]

## generator
+ 在es6中的异步与此基本一致

## 异常

## 文件系统
### 打开文件方式：
```python
try:
	f = open('./a.txt','r')
   print(f.read)
finally:
	if f:
    f.close()
```
但是每次这样try-catch很麻烦，所以：
```python
with open('./a.txt','r') as f:
	print(f.read())
```

### 写文件
在打开文件后，进行write操作即可。

## 常见的内建模块
### datetime

## 虚拟镜像
### 安装虚拟镜像
pip3 install vitualenv 
但是安装后无法使用命令。后通过查询，输入sudo /usr/bin/easy_install virtualenv后即可
 sudo pip install virtualenvwrapper 安装管理，将所有的virtualenv进行管理
  source /usr/local/bin/virtualenvwrapper.sh 将该脚本加入到bash配置中
默认虚拟空间在~/.virtualenvs中

### 创建虚拟空间
 mkvirtualenv myenv

### 使用指定的python版本
mkvirtualenv -p python3.4 venv

### 启动虚拟环境
workon venv

### 退出虚拟环境
deactivate

### 删除虚拟环境
rmvirtualenv

### 查看虚拟环境中的包
pip list 
pip freeze

### 查看虚拟环境
workon 两次tab键

### 进入后安装包
pip install django==1.8.2

## django
### 新建项目 
django-admin startproject myproject
### 添加应用
python manage.py startapp news
### 将应用添加到setting.py中的install中
### 编写model
### 转移model =>python manage.py makemigrations