---
title: SQL学习(一)
date: 2017-06-05 19:07:23
type: "BOCE"
tag: 'boce'
category: 'boce'
---
# sql
sql简介
## 数据库操作
```sql
创建: create database mydb;
使用: use mydb;
删除: drop database mydb;
查看使用的数据库: select database();
将当前目录下的sql文件导入sql数据库：SOURCE ./mysql.sql;
```
## 表的操作
```sql
创建：create table user(id int not null auto_increment primary key，user varchar(20));
插入：insert into user values(0)
删除：drop table user
清空表：delete from table user
查看表结构 describe user
按照表的结构添加数据：insert into user values(0,"alex")
指定添加部分值： insert into user (id) values (1)。即可只插入id
修改表中的元素： update user set user='jack' where id=1, 这样可以将上一步骤中的user设置为jack

```
## 表的编辑
### 删除
```sql
+ 逻辑删除
alter table user add isdelete bit default 0;//更改了表的结构
update user set isdelete=1 where id=1;//给表赋值
+ 物理删除
delete from user where ...
```
### 数据备份
1. sudo su
2. cd /var/lib/mysql
3. mysqldump -uroot -p 数据库名 > ~/user/backup.sql

### 数据恢复
+ mysql -uroot -p 数据库名 < ~/user/backup.sql

##查询操作
### 去重
```sql
select distinct age from user
<!-- distinct 关键字筛选的是整句的条件 -->
select distinct id,age from user
<!-- 必须id和age都不同才算不重复 -->
```
### 逻辑运算符
```sql
select * from user where age=20 and name='jack';
```
### 模糊查询
```
select * from user where name like 'J%';
表示从表中查找已J开头，后面可以有任意位的数据。
select * from user where name like 'J_'
表示查询表中以J开头，后面可以有一位的数据。
```
### 范围查询
```sql
select * from user where id in(1,3,4);
取出id为1,3,4的数据
select * from user where id between 1 and 3;
取出id从1到3的数据

select * from user where age is null;
取出user表中age为null的数据

优先级：
小括号>not>比较运算符>逻辑运算符
```
### 聚合查询
```sql
select count(*) from user;
搜索user表中数据总数
select min(id) from user;
找最小的id,同理有max
select sum(id) from user;
奖user的id加起来，同理有avg(列)求此列的平均值
```
### 分组
```sql
select age, count(*) from user group by age having count(*)>2;
where 是对原始集合进行筛选，having 是对 group by刷选后的值进行筛选。如果使用：
select age, count(*) from user group by age having rs>2; 则筛选出来的结果第二列名为rs
```

### 排序
```sql
select * from user order by age asc(升序) | desc (降序), name asc | desc
先按age行排，然后再按name行排
```

### 分页
```sql
select * from user limit start,count ,
其中start从0开始 ，所以在求第n页数据时，select * from user where age=22 limit (n-1)*m,m
```
### 总结
> select distinct * from TABLENAME where ... group by ... having ... order by ... limit start,count,其中，执行顺序为from TABLENAME ->where ...-> group by ...-> select distinct -> having... -> order by ... -> limit start,count

