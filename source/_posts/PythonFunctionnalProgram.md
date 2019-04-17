---
layout: post
title: Python从入门到深入：函数式编程
date: 2019-4-15 15:00
category: Python
tags: [Python]
description: 介绍Python的函数式编程。其中包含：	
    1. 装饰器，其中给出带参数和不带参数的装饰器使用示例。
---

​	

​	**Python中，每个函数都是一个函数对象，Python中函数也是一个对象，而且函数对象也可以赋值给变量，进而通过变量来调用函数。**且看下面的示例：

​	直接在命令行输入Python否认内置函数abs。

```Python
abs
```

输出如下：

```
<built-in function abs>
```

同时，也可以将一个函数对象赋值给一个参数，通过该参数来调用函数，实现示例如下：

```Python
c = abs(-8)
print(c)			# 8

func = abs
d = func(-8)		# 8
```

但是当我们将Python函数名用作参数，那么该参数将仅用作参数，终端执行示例如下：

```Python
abs=18
abs		  		   # 18
```



### 高阶函数

​	经过上面的示例可以看到，函数可以作为参数传递给另外一个函数。将**一个函数接受另外一个函数作为参数的函数形式，称之为高阶函数。**



#### map/reduce



#### 装饰器	

​	Python中每个函数都有一个```__name__```属性，通过该属性来获取到函数的名称。自定义一个log函数，利用装饰器来获取函数的调用时间和名称。



#### 不带参数的装饰器

首先，定义一个装饰器：

```Python
import time
# 定义一个无参数传入的装饰器
def log(func):
    # wrapper支持任意参数输入的调用
    def wrapper(*args, **kw):
        # 获取当前的时间，之后将当前时间和函数名称打印出来
        curTime = time.strftime('%Y-%m-%d %H:%M:%S',time.localtime(time.time()))
        print("{0} Call {1} ".format(curTime, func.__name__))
        # 调用原始函数
        return func(*args, **kw)
    return wrapper
```

​	上面的log函数接受一个函数输入，并返回一个函数。 我们使用时需要使用Python的@，把decorator置于函数的定义前面。首先，给出一个函数无参数输入的示例：

```Python
# 装饰器的使用，用@将decorator放在函数定义的前面
@log
def decoratorDemo():
    print("Decorator Demo Test")

# 执行函数
'''
2019-04-15 17:07:38 Call decoratorDemo 
Decorator Demo Test 
'''
decoratorDemo()		#等价于log(decoratorDemo)
```

​	然后，给出一个存在参数输入的函数调用示例：

```Python
@log
def decoratorDemo2(name):
    print(name)

'''
2019-04-15 17:07:38 Call decoratorDemo2 
Decorator Demo2 Test
'''
decoratorDemo2("Decorator Demo2 Test")
```



#### 带参数的装饰器

装饰器定义：

```Python
import time
import functools
def log(param):
    def decorator(func):
        @functools.wraps(func)
        def wrapper(*args, **kw):
            curTime = time.strftime('%Y-%m-%d %H:%M:%S', time.localtime(time.time()))
            print("{0} Call {1} with {2}".format(curTime, func.__name__, param))
            return func(*args, **kw)
        return wrapper
    return decorator
```

函数调用示例：

```Python
@log("input")
def paramDemo():
    return ("Param")
'''
2019-04-15 17:42:30 Call paramDemo with input
'''
paramDemo()
```

