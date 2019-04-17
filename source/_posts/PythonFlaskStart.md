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

- **静态路由**

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



- **动态路由**

  URL后面的<>部分对应于URL中的动态部分，该视图适用于生成个人的欢迎消息。使用示例如下：

```Python
@app.route('/next/<info>')
def next(info):
    return '<h1>Next Page is opened with %s </h1>' % info
```

​	在浏览器，输入```http://127.0.0.1:5000/next/hello```，页面显示```Next Page is opened with hello```



- 指定数据类型的动态路由

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



#### 请求-响应循环



##### 程序上下文和请求上下文

​	Flask从客户端接收到请求之后，需要视图函数来访问这个对象，才可以处理相关请求。**请求对象封装和从客户端发送的HTTP请求。**而视图函数访问请求对象的方式之一便是将其作为视图函数的参数传入，而这样会导致每个视图函数都需要增加一个参数，同时如果视图函数处理请求时，需要访问其他的请求对象会很麻烦，由此**Flask引入了上下文，将这些请求对象全局可访问。**

​	Flask分发请求前，激活(或推送)程序和请求上下文，处理完请求之后将其删除。Flask上下文全局变量：

| 变量        | 上下文     | 说明                                               |
| ----------- | ---------- | -------------------------------------------------- |
| current_app | 程序上下文 | 当前激活程序的程序实例                             |
| g           | 程序上下文 | 处理请求时用作临时存储对象，每次请求都会重设该变量 |
| request     | 请求上下文 | 请求对象，封装了客户端发出的HTTP请求内容           |
| session     | 请求上下文 | 用户会话，用于存储请求之间需要“记住”的值的字典     |

- **请求上下文推送之后，可使用request和session变量。**

  ​	request并非真实的全局变量，在多线程中，多个线程处理不同客户端发出的不同请求时，每个线程访问的request是不同，Flask使用上下文让特定的变量在一个线程中全局访问，与此同时不会干扰其他的线程。

  ​	**线程是单独可管理的最小指令集，进程使用多个活动线程，有时会共享内存或文件句柄等资源。多线程web服务器会创建一个线程池，再从线程池中选择一个线程来处理请求。**

```Python
from flask import request

# 引入请求对象处理请求
@app.route('/user/')
def getUserAgent():
    user_agent = request.headers.get("User_Agent")
    return  '<p>Your Brower is %s </p>' % user_agent
```

在Google Chrome访问

```
http://127.0.0.1:5000/user/
```

网页显示：

```
Your Brower is Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/73.0.3683.103 Safari/537.36
```

- **程序上下文被推送之后，在该线程中可以使用current_app和g变量。而直接调用没有激活的程序上下文会导致出错**

```Python
    from flask import current_app
    
    # 获取程序上下文
    app_ctx = app.app_context()
    # 推送程序上下文
    app_ctx.push()
    print("current app name: " + current_app.name)
    app_ctx.pop()
```

结果：

```
current app name: Flask
```

直接调用没激活上下文出错：

```Python
	# RuntimeError: Working outside of application context.
    print(current_app.name)
```



##### 请求调度

P34

##### 请求钩子

P34

Flask支持的四种钩子：

- 

##### 响应

- HTTP状态码
- 返回response对象

```Python
from flask import make_response

@app.route('/res/')
def getRespone():
    respone = make_response('<h1>This Document carries a cookie！</h1>')
    respone.set_cookie('answer', '42')
    return respone
```

- 重定向

  告诉浏览器跳转到新地址，常用于web表单。

```Python
from flask import redirect

@app.route('/res2/')
def getRespone2():
    return redirect('https://www.baidu.com/')
'''
```

- abort，用于错误处理

```Python
from flask import abort

@app.route("/error/")
def getError():
    abort(200)
    return 'Error'
```



#### 扩展



##### Flask-script支持命令行选项

安装Flask-script

```shell
pip install flask-script
```

```Python
if __name__ == '__main__':
    manager = Manager(app)
    manager.run()

```

```shell
# 以调试模式运行服务器，默认5000端口
python Flask.py runserver
# 在40800端口上运行服务器
python Flask.py runserver -p 4800
```

```shell
# 查看帮助信息
python Flask.py runserver --help

optional arguments:
  -?, --help            show this help message and exit
  -h HOST, --host HOST
  -p PORT, --port PORT
  --threaded
  --processes PROCESSES
  --passthrough-errors
  -d, --debug           enable the Werkzeug debugger (DO NOT use in production
                        code)
  -D, --no-debug        disable the Werkzeug debugger
  -r, --reload          monitor Python files for changes (not 100% safe for
                        production use)
  -R, --no-reload       do not monitor Python files for changes
  --ssl-crt SSL_CRT     Path to ssl certificate
  --ssl-key SSL_KEY     Path to ssl key

```



