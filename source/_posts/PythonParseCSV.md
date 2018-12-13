---

layout: post
title: CSV文件解析
date: 2018-11-14 20:00
category: Python
tags: [Python]
description: 介绍了常见的CSV文件的解析的方法。
---

首先，推荐几篇写的很不错的博客：

- [pyhton3-cookbook 读写CSV数据](https://python3-cookbook.readthedocs.io/zh_CN/latest/c06/p01_read_write_csv_data.html#)


### CSV格式

一篇很详细介绍[CSV文件格式的博客](https://blog.csdn.net/woaixiaoyu520/article/details/78455650)，csv文件的字段通过逗号进行分割，其无需指定的软件打开。

- 每一行记录位于单独一行上，用回车换行符CRLF(也就是\r\n)分割
- 每个字段用逗号分割(TSV以制表符分割，TSV是CSV的一种变体)
- 字段中若包含回车换行符、双引号或者逗号，该字段需要用双引号括起来。
- 如果用双引号括字段，那么出现在字段内的双引号前必须加一个双引号进行转义。

首先，我们利用python通过逗号分隔符的形式读取[测试数据](https://github.com/DepInjoy/BaseHouse/blob/master/Python/data/beatles-diskography.csv)

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

在第15行的中，包含逗号分隔符(CSV格式制定了一些相关的处理规则)会导致失败，为避免上述问题使用Python自带的csv可以避免这样的问题。下面介绍一下csv模块操作CSV文件。

#### CSV模块解析CSV文件

DictWriter和DictReader方便于读取和写入字典列表，而reader和writer方便操作列表元素。

##### DictWriter

将一个字典列表写入csv文件。

```Python
headers = ['Math', 'Chinese', 'English']
rows = [
        {'Math':'90', 'Chinese':"80", 'English':'68'},
        {'Math':'83', 'Chinese':'75', 'English':'62'},
      ]
'''
	如果不添加newline=''，写出的csv文件中存在多余一行空白
    而添加newline=''，可以避免写出的csv文件的空白
'''
with open('achievement.csv','w', newline="") as f:
    f_csv = csv.DictWriter(f, headers)
    f_csv.writeheader()
    f_csv.writerows(rows)
```

程序运行的结果如下：

```
Math,Chinese,English,Date
90,80,68,2018/9/1 12:00
83,75,62,2018/9/1 12:00
```

##### DictReader

将上面得到的CSV文件读入一个字典列表。

```Python
import os
import pprint
import csv
def parse_csv(datafile):
    data = []
    with open(datafile, "r") as sd:
        r = csv.DictReader(sd)  		# 为每行创建一个字典，同时将字段名称与表头对应
        for line in r:
            data.append(line)
    return data

if __name__ == '__main__':
    datafile = os.path.join("", "achievement.csv")
    d = parse_csv(datafile)
	for item in d:
		print(item)
    //pprint.pprint(d)
```

#####  运行的结果如下

```
OrderedDict([('Math', '90'), ('Chinese', '80'), ('English', '68'), ('Date', '2018/9/1 12:00')])
OrderedDict([('Math', '83'), ('Chinese', '75'), ('English', '62'), ('Date', '2018/9/1 12:00')])
```

##### reader

CSV文件不是严格的表格，但是文件大致主体的数据部分是由固定的分隔符（如逗号）分隔的，此时可以用```csv.reader```一行一行读取数据，并可以用next跳转到下一行，假设如下的数据格式:

```csv
2018.12.11 record, First Draft
By Sam
Math,Chinese,English
90,80,68
83,75,62
```

```python
import csv
def parse_file(datafile):
    name = ""
    data = []
    with open(datafile,'rt') as f:
        csvReader = csv.reader(f, delimiter=',')
        name = next(csvReader)[1]               #First Draft
        next(csvReader)							#跳过一行
        for row in csvReader:
            data.append(row)
    return (name, data)
```

data部分的数据如下：

```
[['Math', 'Chinese', 'English'], ['90', '80', '68'], ['83', '75', '62']]
```

##### writer

```python
# data = [['Math', 'Chinese', 'English'], ['90', '80', '68'], ['83', '75', '62']]
def save_file(data, filename):
    '''
    	如果不添加newline=''，写出的csv文件中存在多余一行空白
    	而添加newline=''，可以避免写出的csv文件的空白
    '''
    with open(filename, "w"， newline='') as f:
        writer = csv.writer(f, delimiter=',')
        writer.writerow(["2018.12.11 record, First Draft"])
        writer.writerow(["By Sam"])
        for dataItem in data:
            writer.writerow(dataItem)
    f.close()
```

输出的文本内容如下：

```
"2018.12.11 record, First Draft"
By Sam
Math,Chinese,English
90,80,68
83,75,62
```

#### Pandas操作CSV

pandas.read_csv可以将CSV文件中的数据加载到DataFrame中，方便于进行数据的统计、过滤以及其他更高级的操作。

```Python

```







