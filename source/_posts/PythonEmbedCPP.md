---

---

### Python脚本嵌入到C++

​	总是习惯性地在写一小型的demo中给文件命名为test，这次吃亏了，程序中PyObject_GetAttrString函数反悔了NULL,搜索之后才明白，原来是Python中自带test模块，而且优先级高于用户的，后来改名之后运行成功。在实际的项目中大家要注意这一点。首先附上[Python C API官方文档](https://docs.python.org/3/c-api/index.html)​	，推荐几篇博客：

- [在C++软件中嵌入python解释器](https://my.oschina.net/u/2306127/blog/370001)
- [Python实例浅谈之三Python与C/C++相互调用](https://blog.csdn.net/taiyang1987912/article/details/44779719)

#### 手动设置PYTHON_HOME

​	C++项目调用Python脚本，需要你的电脑事先安装了Python，如果你是直接下载的官方release版本的Python，编译时请选择release编译，否则程序编译时由于缺少python27_d.dll而失败。另外编译还需要注意的是如果你安装的是64位的Python，那么你的C++编译器也需要使用64位的，反之32位的也是同样的道理。

​	起初想着已经设置了环境变量其自会到相应的位置加载Python，而在实际使用中每执行到Py_Initialize()这个程序就Crash，经过查阅相关资料，Py_SetPythonHome()将电脑安装的Python路径作为变量传入，crash的问题得到解决，在我个人实践过程中发现，假设Python路径的安装路径为```D:/python```

则针对Python2.7

```C++
Py_SetPythonHome("D:/python");				//传入参数类型为char*
```

而针对Python则

```C++
Py_SetPythonHome(L"D:/python");				//传入参数类型为wchar_t*
```

#### 嵌入Python解释器

​	上面的方式实现了本地调用Python脚本问题，可是直接写死Python的安装路径无疑减少了程序的可扩展性，在博客[如何在没有安装Python环境的客户端，用C调用Python脚本](https://bbs.csdn.net/topics/392092558)了解到在工程中[嵌入Python解释器](https://www.python.org/ftp/python/)的方法，下载完成之后，解压到工程目录下即可。







