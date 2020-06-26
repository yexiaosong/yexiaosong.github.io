---
title: REDIS 学习(nodejs版)
date: 2017-01-09 16:39:20
type: "BOCE"
tag: 'boce'
category: 'boce'
---

## 简介
redis是一个高效的内存key-value数据库，而且可以刷至磁盘
## 安装
+ 电脑安装sudo apt install redis 
+ node添加链接中间件 npm install redis
## 基本使用
### 入门
```javascript
var redis = require('redis');
var client = redis.createClient(6379, 'localhost');
client.set('hello', 'first redis demo');
client.get('hello', function(err, v) {
    console.log('the hello value is: ', err, v)
})
```
如果在createClient的时候传入对象，则保存为字符串。默认调用toString()方法。在后续版本中将报错
### list列表操作(插入元素)
+ lpush rpush...插入操作
+ lrange 读取操作，默认lrange(0,-1)就表示从左取到右
```javascript
//接上述入门程序
client.lpush('testLists', '2');
client.lpush('testLists', '3');
client.lpush('testLists', '4')

console.log(client.lrange('testLists', 0, -1, function(err, list) {
    console.log("the content: ", list);
}));
```
+ lpop左出栈
+ rpop右出栈
### set集合操作(插入不重复数据)
+ sadd集合添加数据
+ smember取出集合中的数据
```javascript
client.sadd('testSet', '10')
client.sadd('testSet', '20')
client.sadd('testSet', '30')

client.smembers('testSet', function(err, v) {
    console.log('the set content: ', err, v);
})
```
### 消息中介
+ publish发送消息
+ subscribe接受消息
```javascript
//publish.js
client.publish('testPublish','111222333')
```
```javascript
//subscribe.js
client.subscribe('testPublish')
client.on('message',function(channel,msg){
    console.log(channel,msg)
})
//on事件必须绑定message事件，每当pub放推个消息，sub端都会手动，可一次作为进程间的通信
```
