---
title: Node学习笔记
date: 2016-10-07 23:39:16
type: "FE"
tag: 'FE'
category: 'FE'
---
# 基础准备
 + es6的新属性
  - let , const定义变量。let声明的代码值在块作用域里有效，同时不会有变量提升。
  - =>箭头式函数，可以不改变this指针。
  - es6有块级作用域
  - 模板字符串，使用两个反引号包起来。``里面可以有回车，换行等。
 + 异步非堵塞的相关知识
 + 正则表达式
  - match函数，stringObj.match(regExp)。有则返回注释部分，没有则返回null。
  ```javascript
var str='aabbccdd';
var res=str.match(/aa/);
console.log(res); //res输出为['aa',index:0,input:'aabbccdd'];
  ```
  - exec函数,regExp.exec(stringObj)
```javascript
var str = 'aabbccdd';
var res = /aa/.exec(str);
console.log(res);//res输出为['aa',index:0,input:'aabbccdd'];
```
  - test函数，regExp.test(str).
  - search函数，stringObj.search(regExp)
  - replace函数，stringObj.search(regExp,replaceTest)
  - split函数，stringObj.split([separator[,limit]]);separator表示分隔符，可以为符号或正则表达式。
    limit表示限制数组返回的个数。
## exec函数和match函数的区别：
 + 方法所有者不同，match属于string，exec属于regaExp
 + match返回值：如果没有使用g(全局匹配)选项，则返回第一个匹配的字符串、
 该字符串所在位置及原始字符串组成的数组，如果使用g选项，则返回所有匹配的字符串组成的数组
 + exec返回值：无论有无g选项，都返回第一个匹配的字符串、该字符串所在位置及原始字符串组成
 的数组，但是该方法可以返回子匹配项，是match所不能及的

# node.js基本概念
## REPL (交互式解释器read eval print loop)
 + _下划线表示获取上一表达式的值。
 + .help表示列出使用命令
## 命令行实质（argv）
```javascript
var args = process.argv.slice(2);
switch (args[0]) {
    case "init":
        console.log('you are init a command');
        break;
    default:
        console.log('what is your meaning');
}
```
+ 可以看出，argv就是你输入的命令并将其以数组方式存放。
## 标准输入输出（stdin stdout）
```javascript
process.stdin.on('data', (input) => {
    input = input.toString().trim();
    process.stdout.write(input + 'nihao');
})
```

# 基本API使用
## node 模块加载顺序，以require(some_module)时的加载顺序
1. 如果是node的核心模块，找到后结束。
2. 如果some_module以"./", "/", "../"时，按路径加载模块，结束。
3. 如果当前目录为current_dir,  按路径加载 current_dir/node_modules/some_module
 + 加载成功，结束。
 + 加载失败，令current_dir为其父目录，继续加载。
 + 重复加载，直到根目录，抛出异常，结束。

## fs模块与path模块
+ fs.readFile
```javascript
"use strict";
const fs = require('fs');
fs.readFile('./a.txt', (err, data) => {
    if (err)
        throw err;
    console.log(data.toString());
})
``` 
+ path
 - path.join(__dirname,'..','a','b.txt): 拼接路径，非常常用。
 - path.basename('a/b/c.txt): 获取路径中的文件名
 - 小实例详询API
+ 综合应用
```javascript
const path = require('path');
const fs = require('fs');
//将文件加载到流中，并在控制台中打印
const buffer = fs.readFileSync(path.join(__dirname, './haha/lrc.txt'));
const content = buffer.toString();
console.log(content);
```
- 但是发现当文本文件为gbk编码时，打印乱码。所以使用第三方库iconv，如下问题解决。
```javascript
const path = require('path');
const fs = require('fs');
const iconv = require('iconv-lite');

const buffer = fs.readFileSync(path.join(__dirname, './haha/lrc.txt'));
var content = iconv.decode(buffer, 'gbk');
console.log(content);
```
- 以流的方式，使用iconv库采用pipe方法，同时使用readline综合实现读取文档
```javascript
const fs = require('fs');
const path = require('path');
const readline = require('readline');
const iconv = require('iconv-lite');

var stream = fs.createReadStream(path.join(__dirname, './a.txt'));
stream = stream.pipe(iconv.decodeStream('GBK'));
const rl = readline.createInterface({
    input: stream,
   // output: process.stdout
});
rl.on('line', (line) => {
    console.log(`received: ${line} \n`);
});

```
## events模块
### 1.events对象中只提供一个对象，events.EventEmitter。它的核心就是事件的封装和触发。
### 2.events对象经典方式实现事件绑定及触发。（不合理）
```javascript
var EventEmitter = require('events').EventEmitter; 
var event = new EventEmitter(); 
event.on('some_event', function() { 
	console.log('some_event 事件触发'); 
}); 
setTimeout(function() { 
	event.emit('some_event'); 
}, 1000); 
``` 
### 3.events对象es6新语法实现事件绑定及触发。（合理）
```javascript
const EventEmitter=require('events');
class MyEmitter extends EventEmitter{};

const myEmitter=new MyEmitter();
myEmitter.on('event',()=>{
	console.log('woca');
});
setTimeout(()=>{
	myEmitter.emit('event');
},1000);
```
### events常见的方法及描述
 + addListener(event,listener);给制定监听器添加一个监听到时间尾部。
 + once(event,listener); 给事件注册一个最多执行一次的监听器
 + removeListener(event,listener);
 + removeAllListener(event,listener);
 + setMaxListener(int);在监听事件过多时会出现错误，通过此方法实现最大值设置。
 + emit(event,[arg1],[arg2],[...]);按顺序执行每个监听器。
## stream流
### 四种流的类型
 + Readable
 + Writable
 + Duplex
 + Transform
### 所有的Stream对象都是EventEmitter对象的实例
 + data---当有数据可读时触发
 + end----没数据可读时触发
 + error--错误时触发
 + finish-结束时触发
 ```javascript
var fs = require("fs");
var data = '';
// 创建可读流
var readerStream = fs.createReadStream('input.txt');
// 设置编码为 utf8。
readerStream.setEncoding('UTF8');
// 处理流事件 --> data, end, and error
readerStream.on('data', function(chunk) {
   data += chunk;
});
readerStream.on('end',function(){
   console.log(data);
});
readerStream.on('error', function(err){
   console.log(err.stack);
});
console.log("程序执行完毕");
 ```
### pipe,非常好用的管道流
+ 基本使用
```javascript
var fs = require("fs");
var readStream=fs.createReadStream('input.txt');
var writerStream=fs.createReadStream('output.txt');
readStream.pipe(writerStream);
console.log('end');
```
+ pipe同事支持链式编程。

 


