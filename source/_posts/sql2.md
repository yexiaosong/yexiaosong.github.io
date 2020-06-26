---
title: SQL学习(二)
date: 2017-09-07 22:39:23
type: "BOCE"
tag: 'boce'
category: 'boce'
---
## 表的关系
### 外键约束
为了防止插入无效数据，使用外键约束，而且添加了几个表之间的关系
```sql
create table scroll(
    id int auto_increment primary key not null,
    foreign key(usrid) references user(id)
);
alter table subject add constraint stu foreign key(stuid) references student(id);
```
### 连接查询
以学生表为例
```sql
select students.nam, subjects.title, scores.score from scores
inner join students on scores.stuid=students.id
inner join subjects on scores.subid=subjects.id; 
```
这样可以将学生表中的分数以姓名，科目和分数三列展示出来，否则会只展示学生和成绩的ID。
+ inner join 连接的表中，在所有的表中都会出现的数据才会展现。
+ left join 以左表为准，此例中将student中的数据全都展现出来。其他位置没有数据以null填充
+ right join 首先将多有表都有的数据展现，后将右边独有的数据填充，左端没有的以null填充

## 视图
为了实现对SQL语句的复用，使用view对语句进行封装
### 试图创建
```sql
create view v_stu as 
select student.*, scores.score from scores
inner join students on scores.stuid = students.id; 
```
### 视图的使用
```sql
select * from v_stu;
```

## 自关联查询
相当于将数据合成大表，再进行选择。(实际使用一张表，但是逻辑上是多个表)

## 事务
### begin ---> commit --->rollback 
begin提交到内存，commit存至硬盘，rollback回到begin之前。相当于java中的进程锁。只要有失败，此次操作就失败。

## 索引
### 原则
+ 避免存NUll，除非有特殊要求
+ 数字比字符串效率更高
+ 连接查询将优先级高的放前面，如=放在范围之前。尽量少用or