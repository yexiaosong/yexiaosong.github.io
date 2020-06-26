---
title: jade(pug) 学习笔记
date: 2017-01-14 18:48:11
type: "FE"
tag: 'FE'
category: 'FE'
---

## jade安装
......
## jade语法
### jade基础语法
#### 大段文本可以使用两种方式
 + 标签下的每一行，tab制表并输入|和空格，再写入文本。
 + 标签后面紧随其后输入.回车，tab制表后输入文本
#### jade数据绑定
```
#{变量}
```
#### jade进行数据传递
 - 命令行方式
 ```cli
jade index.jade -P -w --obj '{"lesson","jade"}'
 ```
 - json方式传递
 ```cli
jade index.jade -P -w -O test.json
 ```
#### jade转义与非转义
+ 使用#{变量}的方式会进行安全转义
+ 使用!{变量}的方式不会转义
#### 流程代码
+ for
```
p for语句用法
        -   var data={"lesson":"jade","lv":"high"}
        -   for(var k in data)
            p=data[k]
//p标签内的内容改为对应对象，出现两个p标签，里面分别是jade和high。
```
+ each
```
p each语句用法
        -   var data={"lesson":"jade","lv":"high"}
        -   each value,key in data
            p #{key}: #{value}
//p标签内lesson:jade,lv:high
```
+ if...else(注意回车和制表符)
```
p if...else
        -   var data={"lesson":"jade","lv":"high"}
        if data
            p has data
        else
            p no data
```
#### mixin语法
mixin相当于代码块
```
mixin lesson
    p lesson jade
+lesson
+lesson
//此时就调用了两次mixin模块
```