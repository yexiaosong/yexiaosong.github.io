---
title: mongoose 学习
date: 2017-01-11 20:01:28
type: "BOCE"
tag: 'boce'
category: 'boce'
---

### mongoose基本使用
```javascript
var mongoose=require('mongoose')
//1.创建连接
mongoose.connect('mongodb://localhost/myBlog')
//2.创建UserSchema方法
var UserSchema=mongoose.Schema({
    nickname:{
        type:String,
        default:'hah'
    },
    creatTime:{
        type:Date,
        default:Date.now()
    }
    
})
//3.创建User数据模型
var User=mongoose.model('User',UserSchema)
//4.创建实例user
var user=new User()
console.log(user);
```
### mongoose的set和get修饰符
+ get 
```javascript
var mongoose=require('mongoose')
mongoose.connect('mongodb://localhost/myBlog')
var UserSchema=mongoose.Schema({
    blog:{
        type:String,
        get: function(url){
            if(!url) return url;
            if(0!==url.indexOf("http://")&& 0!==url.indexOf("https://"))
                url='http://'+url;
                return url;
        }
    }
})
var User= mongoose.model('User',UserSchema);
var user=new User({
    blog:'www.heheda.com'
});

user.save(function(err){
    if (err) {return console.log('save err');}
    console.log('user blog is  '+user.blog);

})
//结果是把网址的协议补充完整，get在数据初始化后、保存文件之前进行的
```
+ set与get用法相同，只是处理的时间不同。
### mongoose的虚拟属性Schema.virtual()
```javascript
var mongoose=require('mongoose')
var PersonSchema=new mongoose.Schema({
    firstName:String,
    lastName:String
});
PersonSchema.virtual('fullname').get(function(){
    return this.firstName+'-'+this.lastName;
})
var Person=mongoose.model('Person',PersonSchema);

var person=new Person({
    firstName:'Alex',
    lastName:'Ye'
});
console.log('user fullname',person.fullname);
```
但是在模型实例转化为JSON属性时，会显示firstName及lastName,没有fullname.
,为了让其显示虚拟属性，需要在PersonSchema.virtual...代码块后面加一句
PersonSchema.set('toJSON',{getter:true,virtual:true});
### mongoose的索引

### mongoose模型的方法
+ 添加静态方法(调用静态方法，使用模型调用即可)
```javascript
var mongoose=require('mongoose')
mongoose.connect('mongodb://localhost/myBlog')
var UserSchema=mongoose.Schema({
    name:String,
    id:Number
})
UserSchema.statics.findById=function(id,cb){
    this.findOne({id:id},function(err,doc){
        cb(err,doc)
    })
}
var User= mongoose.model('User',UserSchema);
var user=new User({
    name:"yezong",
    id:10086
});
user.save(function(err){
    if(err){
        return console.log('save user failed',err);
    }
    User.findById(10086,function(err,doc){
        console.log('findById,err,doc:',err,doc);
    })
})
```
+ 实例方法
```javascript
var mongoose=require('mongoose')
mongoose.connect('mongodb://localhost/myBlog')
var UserSchema=mongoose.Schema({
    name:String,
    id:Number
})
UserSchema.statics.findById=function(id,cb){
    this.findOne({id:id},function(err,doc){
        cb(err,doc)
    })
}
UserSchema.methods.print=function(){
    console.log('the name of the user:'+this.name)
    console.log('the id of the user:'+this.id)
}
var User= mongoose.model('User',UserSchema);
var user=new User({
    name:"yezong",
    id:10086
});
user.save(function(err){
    if(err){
        return console.log('save user failed',err);
    }
    User.findById(10086,function(err,doc){
        console.log('findById,err,doc:',err,doc);
    })
    user.print();
})

```
### 数据校验（在mongoose.Schema对象内声明）
+ required如果分号后为true的话没有数据会报错
+ max min
### 中间件
+ post保存好进行操作
```javascript
var UserSchema=new mongoose.Schema({
    address: String
})
UserSchema.post('save',function(next){
    console.log('sucess');
    next()
})
//操作过程，先保存，在进行console操作，最后next
```
+ pre保存前操作
### ref读取别的数据库（官方文档的Population）
+ DBref
+ populate()
### $运算符
+ 或查询
```javascript
var cond={
    $or:[
    {name:"Alex"}
    {name:"Aston"}
]} 
user.find(cond,function(err,doc){
    if(err)
    //...省略
})
```
