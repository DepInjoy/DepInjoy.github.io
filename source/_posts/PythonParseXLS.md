---
layout: post
title: Excel文件解析
date: 2018-11-15 20:00
category: Python
tags: [Python]
description: 介绍了常见的Excel文件解析的方法。
---

### 利用Python的xlrd模块解析Excel文件

```Python
import xlrd
'''
	获取excel表中最大值
'''
def parse_file(datafile):
    workbook = xlrd.open_workbook(datafile)
    # 读取第1个sheet
    sheet = workbook.sheet_by_index(0)
    data = {}
    row0 = sheet.row_values(0)    # 获取第一行数据
    colNum = sheet.ncols		  # 获取列数
    for i in range(1, colNum-1):
        # 获取第i行从第2行后所有的数据
        colValues= sheet.col_values(i, start_rowx=1, end_rowx=None)
        # 查找列表中最大值
        maxValue = max(colValues)
        # 获取最大值的索引
        maxValueIndex = colValues.index(maxValue)
        # 将excel中的日期转化为年月日时分秒
        year, month, day, hour, minute, second = xlrd.xldate_as_tuple(
            sheet.cell_value(maxValueIndex+1, 0), workbook.datemode)
        data[row0[i]] = [year, month, day, hour, maxValue]
    return data
```

