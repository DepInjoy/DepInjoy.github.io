---
layout: post
title: 文件格式基础
date: 2018-11-14 20:00
category: 数据分析
tags: [数据分析]
description: 介绍了常见的CSV、Excel、Json文件格式以及通过Python解析的方法。
---



### 数据格式

##### CSV格式

一篇很详细介绍[CSV文件格式的博客](https://blog.csdn.net/woaixiaoyu520/article/details/78455650)，csv文件的字段通过逗号进行分割，其无需指定的软件打开。

- 每一行记录位于单独一行上，用回车换行符CRLF(也就是\r\n)分割
- 每个字段用逗号分割(TSV以制表符分割，TSV是CSV的一种变体)
- 字段中若包含回车换行符、双引号或者逗号，该字段需要用双引号括起来。
- 如果用双引号括字段，那么出现在字段内的双引号前必须加一个双引号进行转义。

首先我们利用python通过逗号分隔符的形式读取[测试数据](https://github.com/DepInjoy/BaseHouse/blob/master/Python/data/beatles-diskography.csv)

```python
import pprint
import os

DATADIR = ""
DATAFILE = "beatles-diskography.csv"
def parse_file(datafile):
    data = []
    with open(datafile, "r") as f:
        header = f.readline().split(",")  # 获取表头
        counter = 0
        for line in f:
            if counter == 10:
                break
            fields = line.split(",")
            entry = {}
            for i, value in enumerate(fields):
                entry[header[i].strip()] = value.strip();  # 用strip方法去除空白
                data.append(entry)
            counter += 1
    return data

def test():
    dataFile = os.path.join(DATADIR, DATAFILE)
    res = parse_file(dataFile)
    pprint.pprint(res)

test()
```

在第15行的中，

```

```



```python
import os
import pprint
import csv

DATADIR = ""
DATAFILE = "beatles-diskography.csv"
def parse_csv(datafile):
    data = []
    n = 0
    with open(datafile, "r") as sd:
        r = csv.DictReader(sd)  # 为每行创建一个字典，同时将字段名称与表头对应
        for line in r:
            data.append(line)
    return data

if __name__ == '__main__':
    datafile = os.path.join(DATADIR, DATAFILE)
    d = parse_csv(datafile)
    pprint.pprint(d)
```

