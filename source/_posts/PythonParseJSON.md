---
layout: post
title: JSON文件解析
date: 2018-11-15 20:00
category: Python
tags: [Python]
description: 介绍了常见的JSON文件解析的方法。
---



```Python
import json
def get_from_file(fileName):
    with open(fileName, "r") as f:
        return json.loads(f.read())
```

测试数据：

```
{
    "achieve":[
        {
            "name"  :   "Lucy",
            "age"   :   18,
            "hobby" :   ["Music",   "Reading",    "Running"]
        },
        {
            "name"  :   "Lily",
            "age"   :   20,
            "hobby" :   ["Programming"]
        }
    ]
}
```

输出：

```
{'achieve': [{'name': 'Lucy', 'age': 18, 'hobby': ['Music', 'Reading', 'Running']}, {'name': 'Lily', 'age': 20, 'hobby': ['Programming']}]}
```

