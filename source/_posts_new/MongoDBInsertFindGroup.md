---
layout: post
title: MongoDB数据库创建、插入、查询和聚合分析
date: 2019-8-27 10:49
category: MongoDB
tags: [MongoDB, 数据库, NOSQL]
description: 
---



### 操作数据库

#### 查询数据库

```shell
show dbs										查询当前所有的数据库
```



#### 新建数据库

```shell
use hello										进入hello数据库，如果不存在则创建
```



#### 删除数据库

```shell
db.dropDatabase()								删除数据库
```



#### 复制数据库

```shell
db.copyDatabase("hello", "hello2")				复制数据库
```



### 操作集合

#### 查询集合

```shell
show collections/show tables					显示当前数据库中所有的集合
```



#### 创建集合

```shell
db.createCollection("users")					创建集合
```



#### 删除集合

```shell
db.users.drop()									删除users集合
```



#### 集合重命名

```shell
db.users.renameCollection("users2")				集合重命名

# 将commerce数据库中的users集合重命名为users2
db.adminCommand({renameCollection: "commerce.users", to: "commerce.users2"})
```



### 数据插入

每次插入数据时，如果没有显式地指定字段 ```_id```，则会默认创建一个主键`  _id `。在关系数据库中主键大多数是数值类型,且是自动增长的序列。而MongoDB中的主键值类型则为Objectld类型，这样设计的好处是能更好的支持分布式存储。其中Objectld类型的值由12个字节组成，前面4个字节表示的是一个时间截，精确到秒，紧接着的3个字节表示的是机器唯一标示，接着2个字节表示的进程id，最后3个字节是一个随机的计数器。在 MongoDB 中，每一个集合都必须有一个`_id `字段，不管是自动生成的还是指定的，该值值都必须唯一,如果插入重复值将会抛出异常。

```shell
db.users.save({name:"Ada",age:24})				向当前数据库users表中插入文档
db.users.insert({name:"Lida",age:20})			向当前数据库users表中插入文档
```



为了便于创建excise数据库用于命令练习，之后的MongoDB数据库的学习都是基于此数据库进行：

```
use excise						创建/切换数据库
```

接下来，创建users集合

```shell
db.createCollection("users")
```

制造测试数据，为后续的查询和聚合分析提供数据：

```powershell
db.users.save({name:"ada",age:20,city:"Shanghai"})
db.users.save({name:"Lucy",age:18,city:"Beijing"})
db.users.save({name:"frank",age:16,city:"beijing"})
db.users.insert({name:"xulei",age:17,city:"hangzhou"})
db.users.save({name:"Cidy",age:20,city:"Shanghai",height:158})
db.users.insert([
	{name: "alibaba", age: 25, tags: ["java", "mysql", "mongodb"], city:"Hangzhou"},
	{name: "Lilei", age: 24, tags: ["mongodb"], city:"hangzhou"},
	{name: "Hanmeimei", age:23, tags:["java"],city:"Shenzhen"},
	{name: "Hanhan", age:23, tags:["java", "mongodb"],city:"Shenzhen"},
])

db.users.insert({name:"xulei", orders:[{ title: "mongodb", count: 1 }]})
db.users.insert([
{name: "Lily",orders : [ { title: "mongodb", count: 1 }, { title: "java", count:3 } ] },
{name: "Zhangsan", orders : [ { title: "mongodb", count: 1 }]},
{name : "Lisi", orders : [ {title : "RR", count : 1 },{title : "Benz", count: 10 } ] }]);
```



### 查询语言系统

​	在 MongoDB 中，查询通常针对一个集合来操作。查询可以指定查询条件，只返回匹配的文档；可以指定投影项，只返回指定的字段，减少返回数据到客户端的网络流量。



#### 查询选择器

最简单的查询语句为

```shell
db.users.find() 								查询当前数据库users表中所有数据
db.users.find({}) 								查询当前数据库users表中所有数据
```



精确匹配选择器:

```shell
db.users.find({age:24})							查询age=24的文档
db.users.find({name:"Lilei",age:24})			查询同时匹配name为Lilei且age为24的文档
db.users.find({age:{$lt:24}})					查找age<24的文档
db.users.find({age:{$lte:24}})					查找age<=24的文档
db.users.find({age:{$gt:24}})					查找age>24的文档
db.users.find({age:{$gte:24}})					查找>=24的文档
db.users.find({age:{$lte:24}, name:"ada"})		查询同时匹配age<=24且name=ada的文档
db.users.find({"orders.count": {$lt: 2}})		嵌套文档查询orders.count<2的文档
```



范围选择器：

	- $in表示返回key的值在某些value范围内
	- $nin 表示返回key值不在某些value范围内，$nin是一种比较低效的査询选择器，会进行全表扫描，因此最好不要单独使用 $nin。
	- $ne表示不等于。如果单独使用$ne，它不会利用索引优势，反而会进行全表扫描，最好与其他查询选择器配合使用。

```shell
db.users.find({age:{$gt:16,$lt:24}})				查询18<age< 24的文档
db.users.find({"orders.count": {$gt: 0,$lte: 100}})	查询0<orders.count<=100的文档

db.users.find({age:{$in:[24,16]}})					查询age=24和32的文档
db.users.find({age:{$nin:[24,16]}})					查询age=24和32的文档
db.users.find({age:{$ne:24}})						查询age不等于24的文档
db.users.find({ $or:[{age: 18}, {name:"frank"}]})	或运算运算器，查找age=18或name为frank文档
db.users.find({$or: [{"orders.title" : "java"}, {"orders.count":1}]})
													嵌套文档的or运算
db.users.find({ $and:[{age: 18}, {name:"frank"}]})	与运算选择器，查找age=18或name为frank文档
db.users.find({age:{$exists:false}})				查询不包含age字段的文档
db.users.find({age:null})							查找不包含age字段的文档，和上一语句等价
db.users.find({age:{$exists:true}})					查询包含age字段的文档
```



#### 查询投射

​	查询选择器用来返回匹配的文档集，有时我们需要对返回的结果集作进一步处理，如用查询投射进行只需要返回指定的字段的处理。

```shell
db.users.find({age:18}, {_id:0, name:1,age:1})		查找age=18，过滤掉默认的_id,只返回name和age字段
db.users.find({age: { $gt: 18 }}, {_id:0, name:1})	查找age大于18，并过滤_id,返回name字段
db.users.find({age:{$lt:24}}, {name:0})				不显示age<24的排除name字段信息
```



对查询结果集进行升序列或者降序排序，排序是很浪费时间的，对于排序比较重要的一点是确保排序的字段上建立了索引，且排序索引计划可以高效地利用索引。

```shell
db.users.find().sort({age:1})					按照age升序排列
db.users.find().sort({age:-1})					按照age降序排列
db.users.find().sort({age:-1}).pretty()			按照age降序排列并优化结果集显示
db.users.find().skip(1).limit(5).sort({age:1})  对升序排列结果跳过第一条，返回紧随其后的5条结果
```



#### 数组操作

如果值为数组类型并且数组的元素又是文档类型，查询语句将会有所变化，类似如下的数据结构：

```
{
	"_id" : ObjectId("5d65ec8918b7f8494535cd69"),
	"name" : "alibaba",
	"age" : 25,
	"tags" : [
		"java",
		"mysql",
		"mongodb"
	],
	"city" : "Hangzhou"
}
```

- 精确匹配数组元素
```
db.users.find({tags: ["java", "mongodb"]})
```
- 嵌套文档的精确匹配数组元素
```shell
db.users.find({"orders": {title: "java", count: 3}})
```

- 无顺序不精确匹配:
```
db.users.find({tags: {$all:["java", "mongodb"]}})
```
- 匹配数组中的一个元素，假设数组中有多个元素，只要有一个元素符合条件，该文档便会返回。
```
db.users.find({tags: "java"})
```

- 匹配指定位置的元素
```shell
db.users.find({"tags.0": "mongodb"})		查询tags元素中第一个元素为mongodb的文档
```

- 指定数组索引并匹配嵌套文档中的字段值，其中嵌套文档一个数据记录展示如下：

```shell
{
	"_id" : ObjectId("5d6613ae40ad584885505fff"),
	"name" : "Lisi",
	"orders" : [
		{
			"title" : "RR",
			"count" : 1
		},
		{
			"title" : "Benz",
			"count" : 10
		}
	]
}
```

```shell
# 查询数组索引0的title为mongodb的文档
db.users.find({"orders.0.title": "mongodb"})

# 查询数组索引0的title为mongodb文档,只返回orders中title和count的数据
db.users.find({"orders.0.title": "mongodb"}, {"orders.title":1, "orders.count":1})

# 查询数组索引0的title为mongodb文档，返回最后一个元素的orders.title数据
db.users.find({"orders.0.title": "mongodb"}, {orders: {"$slice":-1},"orders.title":1})	
```

- 组合条件同时满足条件过滤
```shell
# 查询同时满足title为mongodb且count>=1的文档
db.users.find({orders: {"$elemMatch":{title:"mongodb", count:{$gte: 1}}}})
```



```shell
db.users.find({name:/.*a.*/})					a字母所在位置不限定
db.users.find({name:/^a.*/})					a字母开头
db.users.find({name:/.*a$/})					a字母结尾

db.users.count()								查询数量
db.users.find({age:24}).count()					查询age=24的数量
```



### 修改语句

​		在mongoDB中通过update进行数据的修改，update也支持一些可选参数，修改可以划分为两种：一种是只针对具体的目标字段,其他不变;另一种是取代性的更改,即修改具体目标字段后,其他的字段会被删除。update语法格式如下：

```
db.collection.update(criteria,objNew,upsert,multi)

参数说明：
:param criteria:	查询条件
:param objNew:		update对象和一些更新操作符
:param upsert:		若不存在update记录，是否插入objNew文档，true为插入，false为不插入，默认为false。
:param multi:		默认是false，只更新找到的第一条记录；如果为true，把按条件查询出来的记录全部更新。
```



更改指定字段

```shell
# 将从users集合中age=32的第一条记录name更改为Lucy
db.users.update({"age":18},{$set:{"name":"Lucy"}})

# 将users集合中name为Cidy的height增加8
db.users.update({name:"Cidy"},{$inc:{height:8}})
```

更改指定字段，其他字段被删除

```shell
# 将users集合中name为Cidy的文档，更改为height:158
db.users.update({name:"Cidy"},{height:158})
```

更改多个文档的指定字段

```shell
# 将从users集合中age=32的所有记录name更改为Lucy
db.users.update({"age":18},{$set:{"name":"Lucy"}}， false, true)
```

更改文档指定字段若不存在则插入

```shell
# 从users集合中age=30的所有记录name更改为Lucy，如果不存在age=30则插入{"age":30， "name":"Lucy"}
db.users.update({"age":30},{$set:{"name":"Lucy"}}， true)
```



### 删除文档

在MongoDB中通过remove进行删除操作，remove的语法格式如下：

```shell
db.collection.remove(criteria,justOne)

参数说明：
@param criteria:	删除文档的条件，可选
@param justOne:		默认为false，若设为false，则删除所有符合匹配条件的文档，设为true或1，则只删除
					匹配到的第一个文档。
注意事项：如果remove没有指定任何参数，将删除集合中的所有文档，但是不会删除集合对应的索引数据。如果想删除集合中的所有文档,同时也删除集合的索引，可以使用drop方法。
```

```shell
db.users.remove({age:23},1)						删除age=23的第一个文档
db.users.remove({"age":23})    					删除age=23的文档
db.users.remove({})								删除users集合除了集合索引以外的所有文档
db.users.remove()								删除users集合所有的文档
```



### 聚合分析

mongoDB支持的数据聚合方法有聚合管道、聚合函数、MapReduce。



#### 过滤

查询所有的数据去重：

```shell
db.users.distinct("name")								返回不重复的name
db.users.distinct("orders.title")						嵌套文档去重
```

条件查询后去重：

```shell
db.users.distinct("name",{"orders.title":"mongodb"})	查询orders.title为mongodb，返回name信息
```



#### 分组Group



##### 聚合管道

通过db.collection.aggregate() 实现聚合操作。



##### MapReduce聚合

2.2版本超过2万个文档,建议使用: db.collection.mapReduce()



##### group函数分组

2.4版本以后支持group进行分组，通过JavaScript lock,阻塞其他JavaScript执行。group语法格式如下：

```
db.collection.group({ key, reduce, initial[, keyf] [, cond] [, finalize] })

参数说明：
@param key					分组关键字
@param reduce 				初始化变量，有两个参数：当前文档和结果文档。
@param cond 				过滤条件
@param finalize
```

- 1 统计各个年龄段的用户数

```shell
db.users.group(
	{
		key:{age:1},
		reduce:function(curr,result){
			result.total += 1;
		},
		initial:{total:0}
	}
)
```

- 2 统计age大于18的各个年纪的用户数（不同年龄段用户统计）

```shell
db.users.group(
	{
		key:{age:1},
		cond:{age:{$gt:18}},
		reduce:function(curr,result){
			result.total += 1;
		},
		initial:{total:0}
	}
)
```

- 3 统计所有age大于18岁的用户

```shell
db.users.group(
	{
		key:{},
		cond:{age:{$gt:18}},
		reduce:function(curr,result){
			result.total += 1;
		},
		initial:{total:0}
	}
)
```