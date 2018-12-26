---
layout: post
title: Python访问MongoDB数据库
date: 2018-8-19 20:00
category: MongoDB
tags: [MongoDB、 Python、NoSQL、数据库]
description: 通过Python访问MongoDB数据库中的数据，展示了数据库、集合的创建、删除以及数据的增删改查等等操作的实现。
---

​	Python提供的pymongo模块访问MongoDB数据库。[Python访问MongoDB](http://api.mongodb.com/python/)

下面给出一些小的示例来介绍如何实现操作的。

## 创建数据库和集合

#### 创建数据库

```Python
from pymongo import MongoClient
def get_db(dbName):
    '''
    获取数据库，如果数据库不存在则新建该数据库。
    打印显示相应服务器端的数据库名称
    :param dbName:              数据库名称
    :return:                    数据库
    '''
    client = MongoClient('localhost:27017')
    # 如果不存在名称为dbName的数据库则创建，返回该数据库
    db = client.get_database(dbName)

    # 获取所有的数据库名称，创建的数据库只有在插入数据后，该数据库才创建
    for database in client.list_database_names():
        print(database)
    return db
```

### 创建集合

```Python
'''
获取名称为name的集合，如果不存在则新建该集合
'''
collection = db.get_collection(name)
```

## 数据增删改查投影

#### 增加数据

```Python
def add_data(db, name):
    '''
    向数据库添加集合并插入数据
    :param db:              数据库
    :param name:            集合名称
    '''
    collection = db.get_collection(name)
    collection.insert({"name": "Chicago", 'nation' : 'USA'})
```

### 查询数据

```Python
def get_data(db, name, cod):
    '''
    获取数据库集合中符合某种条件的数据
    :param db:              数据库
    :param name:            集合名称
    :param cod:             查找条件
    '''
    doc = None
    if name not in db.collection_names():
        print(name + "is not in this database.")
    else:
        doc = db.get_collection(name).find(cod)
    return doc

def get_all(db, name):
    cursor = db.get_collection(name).find()
    for doc in cursor:
        print(doc)
# 示例
data = get_data(db, "cities", {"nation": "USA"})
for item in data:
	print("get data ", item)
```

### 修改数据

#### update_one和update_many

```Python
def updateData(db, colName, cod, value, single = False):
    '''
    更改指定集合中符合某种条件的数据
    :param db:              数据库
    :param colName:         集合名称
    :param cod:             查找条件
    :param value:           更新数值
    :param single:          True:只更新查找到的第一个数据值，False:更新查找到所有符合条件的数值
    '''
    cursor = db.get_collection(colName)
    if single:
        cursor.update_one(cod, value)
    else:
        cursor.update_many(cod, value)
# 示例
updateData(db, "cities", {"age": 24}, {'$set':{"name":"Lily"}}, True)
updateData(db, "cities", {"age": 24}, {'$set': {"name": "Jack"}})
```

#### save

```Python
def saveData(db, colName, cod, key, value, single = False):
    cursor = db.get_collection(colName)
    if single:
        doc = cursor.find_one(cod)
        if doc is not None:
            doc[key] = value
            cursor.save(doc)
# 调用示例
saveData(db, "cities",{"nation": 'USA'}, "name", "Lucy", True)
```

#### update

```Python
# 复位
col = db.get_collection("cities")
doc = col.update({"name": "Lucy"}, {'$unset': {"age": ""}})

#多个文档更新
doc = col.update({"nation": 'USA'}, {'$set': {"name": ""}}, multi=True)
```

### 删除数据

```Python
def rmData(db, colName, cod):
    '''
    删除指定名称集合中符合条件的一个文档
    :param db:              数据库
    :param colName:         集合名称
    :param cod:             删除条件
    '''
    cursor = db.get_collection(colName)
    cursor.delete_one(cod)

def rmMany(db, colName, cod):
    '''
    删除指定名称集合中符合条件的多个文档
    :param db:              数据库
    :param colName:         集合名称
    :param cod:             删除条件，若为{}则删除所有文档
    '''
    cursor = db.get_collection(colName)
    cursor.delete_many(cod)
# 示例
rmData(db, "cities", {"nation": "USA"})
rmMany(db, "cities", {"nation": "USA"})
rmMany(db, "cities", {})
```

#### 文档删除

```Python
col = db.get_collection("cities")
col.remove({"name":"Tornaduo"})				#删除name=Tornaduo的文档
col.remove({"age" : {"$exists" : 1}})		#删除存在age字段文档
col.remove({"nation": {"$exists": 0}})		#删除不存在nation字段的文档
```



### 投影

```Python
cursor = db.get_collection(name).find({}, {'nation' : 1}
```

