---
layout: post
title: Flask入门
date: 2019-4-16 18:04
category: Flask
tags: [Flask, Python, 服务器]
description: 
---

​	首先，[Flask](http://flask.pocoo.org/)是一个可扩展的微服务器框架，它包含一个基本服务核心，其他的功能可以通过挑选任意符合项目需求的扩展来实现，这和大型框架不同，大型框架往往已经替开发者做出了大多数的选择，很多时候难以有替换方案。Flask主要有两个依赖：

- 路由、调试和Web服务器网关接口（Web Server Gateway Interface，WSGI）子系统由[Werkzeug](https://palletsprojects.com/p/werkzeug/)提供。
- 模板系统由[Jinja2](http://jinja.pocoo.org/)提供。

### 环境搭建

​	使用Flask最方便的是使用虚拟环境，虚拟环境是Python的一个副本，在这个环境中，我们可以安装一些包而不影响整体的环境。

```shell
pip install virtualenv
```

激活环境

```shell
cd venv/Scripts
activate
```

退出虚拟环境

```
deactivate
```



### Flask Web程序

#### 程序结构

```Python
from flask import Flask

'''
创建程序实例
Web服务器使用 WSGI协议吧接收的来自客户端的请求都在转交给该对象处理
'''
app = Flask(__name__)

'''
定义路由
Flask定义路由最简单的方式就是利用实例提供的app.route修饰器，将修饰器函数注册为路由
'''
@app.route('/')
def index():
    '''
    视图函数
    index()作为程序根地址处理程序，这样通过web浏览器访问时，会自动执行index函数
    该index函数名可以更换为hello或者其他
    '''
    return '<h1>Hello World</h1>'

if __name__ == '__main__':
    '''
    启动服务器
    '''
    app.run()
```



#### 路由和视图

​	客户端(如：Web浏览器)将请求发送给服务器，服务器将请求给Flask，Flask实例根据URL来决定于宁哪些代码。Flask保存了URL和函数之间的关系，**确定URL和函数之间关系的程序称之为路由。**



##### 实现路由方式一：修饰器函数注册为路由

​	像index()这样的函数称为**视图函数，视图函数的返回值称为响应**，即客户端收到的内容。

- 地址无可变部分

```Python
@app.route('/')
def index():
    '''
    视图函数
    index()的返回值为响应，客户端收到的内容
    将index()作为程序根地址处理程序，这样通过web浏览器访问时，会自动执行index函数
    该index函数名可以更换为hello或者其他
    '''
    return '<h1>Hello World</h1>'
```



- 地址包含动态可变部分

  URL后面的<>部分对应于URL中的动态部分，该视图适用于生成个人的欢迎消息。使用示例如下：

```Python
@app.route('/next/<info>')
def next(info):
    return '<h1>Next Page is opened with %s </h1>' % info
```

​	在浏览器，输入```http://127.0.0.1:5000/next/hello```，页面显示```Next Page is opened with hello```



- 地址中指定动态可变部分的数据类型

  URL的动态部分默认使用字符串，同时也支持定义指定类型，如int， float和path，其中path也是字符串，但是其不将斜杠视作分隔符，而是将其视作动态片段的一部分。

  使用示例如下

```Python
@app.route('/user/<int:id>')
def user(id):
    return '<h1>Input id: %s </h1>' % id
```

​	在浏览器，输入```http://127.0.0.1:5000/user/1```，页面显示```Input id: 1```

​	在浏览器，输入```http://127.0.0.1:5000/user/a```，页面显示

```
Not Found
The requested URL was not found on the server. If you entered the URL manually please check your spelling and try again.
```



#### 启动服务器

​	服务器启动之后，进入轮询，等待并请求处理，轮询将会一直进行，直至程序终止。

```Python
'''
确保只有执行该脚本才会启动Web服务器
如果该脚本由其他脚本引入，则不会出现运行app.run()导致启动服务器
'''
if __name__ == '__main__':
    app.run()
```



##### web服务器的操作模式

- 调试模式，将```debug=True```

  ```Python
  app.run(debug=True)
  ```

- 激活调试器

- 重载程序

