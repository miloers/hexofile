---
title: mongoDB初探 基本命令
date: 2017-08-03 13:22:27
tags: mongoDB
---

![](http://upload-images.jianshu.io/upload_images/2542851-5df22c57e288a8ac.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240
)

<!--more--><!--more-->
<strong>新建/选择数据库</strong>

>use test

<strong>显示数据库列表</strong>
>show dbs

<strong>创建表</strong>
>db.createCollection("test")

<strong>展示表</strong>
>show collections

<strong>删除当前数据库</strong>
>db.dropDatabase()

<strong>获取当前环境的数据库名</strong>
>db.getName()

<strong>数据库命令帮助</strong>
>db.help()

<strong>删除集合</strong>
>db.name.remove()

<strong>集合命令帮助</strong>
>db.name.help()

#### 文档操作查询

<strong>查询所有</strong>
>db.name.find()

<strong>查询第一条记录</strong>
>db.name.findOne()


<strong>按条件查找</strong>
>db.name.find({"name":"miloer"})

<strong>指定返回值查询</strong>
>db.name.find({},{"name":1})

{}代表所有
name代表所查字段
1：代表所查询的数据中包含name属性
0：与1相反

<strong> $ gt  查询name表中年龄大于20的数据</strong>
>db.name.find({"age":{$gt:20}})

<strong> $ gte  查询name表中年龄大于等于20的数据</strong>
>db.name.find({"age":{$gte:20}})

<strong> $ lt  查询name表中年龄小于20的数据</strong>
>db.name.find({"age":{$lt:20}})

<strong> $ lte  查询name表中年龄小于等于20的数据</strong>
>db.name.find({"age":{$lte:20}})

<strong>$ or 查询name表中满足年龄等于20 或者21的数据</strong>
>db.name.find({$or:[{age:20},{age:21}]})

注意插入的age对象必须为number类型。我第一次插入的是字符串，就没查出来（滑稽）.


#### 过滤查询

<strong>包含查询</strong>
查询表中name属性包含test的文档
>db.name.find({name:/test/})

<strong>包含头查询</strong>
查询表中name属性开头包含'test'的文档
>db.name.find({name:/^test/})


#### 排序查询

<strong>升序</strong>
>db.name.find().sort({age:1})

<strong>降序</strong>
>db.name.find().sort({age:-1})


#### 限定查询条数

<strong>skip</strong>

限定起始查询条（从第五条开始查询）

>db.name.find().skip(5)


<strong>limit</strong>

限定查询5条（查询至第五条为止）

>db.name.find().limit(5);

#### 文档操作 增、删、改

##### 增

######  insert 插入文档

<strong>单条插入</strong>
>db.name.insert({username:'test'})

<strong>批量插入</strong>
>var query = [{'username':1},{'username':2}];
>db.name.insert(query)

<strong>池插入</strong>
>var query = [{'username':1},{'username':2}];
>db.name.bathInsert(query);

###### save
save 与insert类似，insert插入时如果存在主键会报错，save插入如果存在会覆盖。效率比insert要低。

##### 删

<strong>删除指定文档</strong>
>db.name.remove({'userName':1})


<strong>删除指定文档中的第一条</strong>
>db.name.remove({'userName':1},{justOne:true})

<strong>删除所有文档</strong>
>db.name.remove({})

##### 更新

<strong>$set</strong>

>db.name.update({'userName':1},{$set:{'userName':2}})

<strong>$inc</strong>
自增 ,agree+=10

>db.name.update({userName:'test'},{$inc:{agree:10}})

