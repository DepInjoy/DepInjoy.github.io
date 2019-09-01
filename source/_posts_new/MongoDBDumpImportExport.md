---
layout: post
title: MongoDB数据库管理备份和数据导入导出
date: 2019-8-28 10:49
category: MongoDB
tags: [MongoDB, 数据库, NOSQL]
description: 
---



mongodump备份和mongorestore恢复

### 数据备份/恢复

数据备份的原则：

- 1.  标记文件来识别备份的内容和备份反映的时间点。

- 2. 使用--oplog参数在mongodump备份期间捕获传入的写操作，以确保数据备份反映一致的数据状态。

- 3. 如果mongodump和mongorestore对性能影响太大，请使用其他备份策略，例如文件快照或MongoDB Cloud Manager。

- 4. 测试备份MongoDB部署,确保备份可用。

#### 1. 磁盘阵列

#### 2. 文件快照

Windows和Linux系统差异比较大。

#### 3.直接复制数据文件

 **方式一:** 

- 1. 首先锁定数据库，禁止数据写入，并进行同步讲所有的脏页面刷新至硬盘，来确保目录中所有的文件都是最新的，且不会被更改。

     ```shell
     db.fsyncLock()
     ```

- 2. fsyncLock返回后，复制数据库中所有的文件到备份的位置。

     ```shell
     sudo cp -R ./data/db  ./data/backup/
     ```

- 3. 数据复制完成，解锁数据库，使其能够再次写入。

     ```shell
     db.fsyncUnlock()
     ```



 **方式二：**

先将mongod关闭，讲数据库中文件复制到备份位置，重启mongod服务。



#### 4. mongodump工具

​		mongodump是MongoDB官方提供的数据备份工具，可以从mongod或mongos实例导出二进制BSON备份数据。它可以用于转存整个数据库、集合或查询结果，也可以备份正在写入的数据库附带备份oplog，比较适用于小型数据备份，不适合大型系统。

- 可以直接操作底层数据文件。
- 默认，mongodump不捕获local数据库的内容。
- 为了节省空间，备份不包括索引信息，如果恢复数据库需要重建索引。
- 采用的资源密集型的，非增量的方法，会对mongod性能、系统性能产生影响。
- 备份文件的格式是xxxx.bson和xxxx.metadata.json两种文件。

```shell
./mongodump --host 127.0.0.1 --port 27017 --out ../data/backup --collection taobao1111 --db commerce

参数说明：
--host					主机地址
--port					服务器端口号
--out					备份文件存放地址
--collection			集合名
--db					数据库名
--user					登录用户名
--password				登录密码
--query					添加过滤条件
--oplog					在备份过程中捕获oplog更改日志，以保持一致的时间点
```



#### 4. mongoimport/mongoexport导入/出工具

#### 5. MongoDB Atlas云计算平台

#### 6. MongoDB Cloud Manager

#### 7. Ops Manager企业版收费

#### 8. 第三方工具



### 数据恢复

#### 1.  基于Dump备份文件的数据恢复

​	该方式对当前数据库进行恢复可能会出现主键重复的问题，针对这个问题的解决方式是将当前存在的数据库销毁。另外，原数据库如果存在索引，需要重新建立索引。

```shell
sudo ./mongorestore --host 127.0.0.1 --port 27017 ../data/backup/

参数说明：
--host					主机地址
--port					服务器端口号
--oplogReplay			重放捕获的oplog，需要执行mongodump带--oplog参数
```



####  2.  基于文件快照的数据恢复

```

```



#### 3.  基于直接复制数据库的数据恢复

```shell
sudo ./mongod --dbpath ../data/backup/db
```



### 数据导入

​		mongoimport是从外部向MongoDB数据库导入格式为json、csv、tsv数据的独立的导入工具，常用参数如下：

```shell
mongoimport --db dbName --collection colName --headerline --type csv --file filePath

参数说明：
--db/-d 				数据库名
--collection/-c			集合名
--type					文件类型
--headerline			指明第一行是列名，不需要导入
--file					文件路径和名称
```



### 数据导出

​		mongoexport是将MongoDB数据库中的数据导出为json、csv、tsv格式的工具。

```shell
./mongoexport --db commerce --collection taobao1111 --out 双十一淘宝美妆.json

参数说明：
--db/-d 				数据库名
--collection/-c			集合名
--out					导出文件的路径和名称，默认是stdout
```

使用过程中的注意事项：

- **避免使用mongoimport和mongoexport进行完整的实例备份。**
- **不能确保所有BSON数据类型,因为JSON只能表示BSON类型子集。**





-----------------------------------

> use commerce
> switched to db commerce
> db.createCollection("taobao1111")
> { "ok" : 1 }
>
> mongoimport --db commerce --collection taobao1111 --headerline --type csv --file /home/xy/project/code/DataAnalysis/mongodb/双十一淘宝美妆.csv
> connected to: 127.0.0.1
> 2019-08-27T13:45:59.987+0800 check 9 27599
> 2019-08-27T13:46:00.273+0800 imported 27598 objects
>
> 修改列名
> db.taobao1111.update({}, {$rename :{"update_time" : "日期"}}, false, true)
>
> db.taobao1111.update({}, {$rename :{"id":"商品id"}}, false, true)
>
> db.taobao1111.update({}, {$rename :{"title":"商品名称"}}, false, true)
>
> db.taobao1111.update({}, {$rename :{"price":"价格"} }, false, true)
>
> db.taobao1111.update({}, {$rename :{"sale_count":"销售量"}}, false, true)
>
> db.taobao1111.update({}, {$rename :{"comment_count":"评价数量"} }, false, true)
>
> db.taobao1111.update({}, {$rename :{"店名" : "品牌名称"}}, false, true)
>
> 
>
> 不同品牌的数量
>
> db.taobao1111.group({
> ​		key:{"品牌名称":1},
> ​		reduce:function(curr,result){
> ​			result.total += 1;
> ​		},
> ​		initial:{total:0}
> })
>
> [
> ​	{
> ​		"品牌名称" : "自然堂",
> ​		"total" : 1190
> ​	},
> ​	{
> ​		"品牌名称" : "资生堂",
> ​		"total" : 821
> ​	},
> ​	{
> ​		"品牌名称" : "植村秀",
> ​		"total" : 750
> ​	},
> ​	{
> ​		"品牌名称" : "悦诗风吟",
> ​		"total" : 3021
> ​	},
> ​	{
> ​		"品牌名称" : "玉兰油",
> ​		"total" : 1135
> ​	},
> ​	{
> ​		"品牌名称" : "雅漾",
> ​		"total" : 663
> ​	},
> ​	{
> ​		"品牌名称" : "雅诗兰黛",
> ​		"total" : 1810
> ​	},
> ​	{
> ​		"品牌名称" : "雪花秀",
> ​		"total" : 543
> ​	},
> ​	{
> ​		"品牌名称" : "相宜本草",
> ​		"total" : 1313
> ​	},
> ​	{
> ​		"品牌名称" : "薇姿",
> ​		"total" : 746
> ​	},
> ​	{
> ​		"品牌名称" : "倩碧",
> ​		"total" : 1704
> ​	},
> ​	{
> ​		"品牌名称" : "欧珀莱",
> ​		"total" : 1359
> ​	},
> ​	{
> ​		"品牌名称" : "欧莱雅",
> ​		"total" : 1974
> ​	},
> ​	{
> ​		"品牌名称" : "妮维雅",
> ​		"total" : 1329
> ​	},
> ​	{
> ​		"品牌名称" : "蜜丝佛陀",
> ​		"total" : 434
> ​	},
> ​	{
> ​		"品牌名称" : "美加净",
> ​		"total" : 1678
> ​	},
> ​	{
> ​		"品牌名称" : "美宝莲",
> ​		"total" : 825
> ​	},
> ​	{
> ​		"品牌名称" : "兰芝",
> ​		"total" : 1091
> ​	},
> ​	{
> ​		"品牌名称" : "兰蔻",
> ​		"total" : 1285
> ​	},
> ​	{
> ​		"品牌名称" : "娇兰",
> ​		"total" : 1193
> ​	},
> ​	{
> ​		"品牌名称" : "佰草集",
> ​		"total" : 2265
> ​	},
> ​	{
> ​		"品牌名称" : "SKII",
> ​		"total" : 469
> ​	}
> ]



打折情况分析

```
db.createCollection("discount")
db.taobao1111.find({"日期": "2016/11/11"})
```





附录

- ```
  如果excel里面有中文、特殊符号，会抛出以下异常：exception:Invalid UTF8 character detected
  此时，执行mongoimport命令前，您需要先将该csv文件编码转为 utf-8
  方法：将 PITags.csv 上传到 Linux系统，然后利用iconv 命令转换编码：
  iconv -f gbk -t UTF-8 PITags.csv  > PITags2.csv
  ```