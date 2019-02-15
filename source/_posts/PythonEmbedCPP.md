---

---

### Python脚本嵌入C++

​	总是习惯性地在写一小型的demo中给文件命名为test，这次吃亏了，程序中PyObject_GetAttrString函数反悔了NULL,搜索之后才明白，原来是Python中自带test模块，而且优先级高于用户的，后来改名之后运行成功。在实际的项目中大家要注意这一点。

​	C++项目调用Python脚本，需要你的电脑事先安装了Python，如果你是直接下载的官方release版本的Python，编译时请选择release编译，否则程序编译时由于缺少python27_d.dll而失败。另外编译还需要注意的是如果你安装的是64位的Python，那么你的C++编译器也需要使用64位的，反之32位的也是同样的道理。起初没有调用Py_SetPythonHome来设置Python所在路径，想着已经设置了环境变量其自会到相应的位置加载Python，而在实际使用中每执行到Py_Initialize()这个程序就Crash，可是直接写死Python的安装路径无疑减少了程序的可扩展性，这部分如何优化待后续查阅资料补充。

[Python C API官方文档](https://docs.python.org/3/c-api/index.html)

