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

[C++调用Python脚本实现加法的完整代码](https://github.com/DepInjoy/BaseHouse/blob/master/CPP/CallPython.cpp)

#### 嵌入Python解释器

​	上面的方式实现了本地调用Python脚本问题，可是直接写死Python的安装路径无疑减少了程序的可扩展性，在博客[如何在没有安装Python环境的客户端，用C调用Python脚本](https://bbs.csdn.net/topics/392092558)了解到在工程中[嵌入Python解释器](https://www.python.org/ftp/python/)的方法，下载完成之后，解压到工程目录下即可。

##### 将C++中list传递给Python

[实现代码包含将Python3.6嵌入到其中](https://github.com/DepInjoy/BaseHouse/blob/master/CPP/CPPCallPythonDemo/main.cpp)

##### 获取Python返回的字典

​	Python函数返回的字典个数为1或者大于1个，可以通过PyTuple_Size函数获取字典的个数，然后依次获取字典中的元素，读取字典中的内容。需要我们注意的是，如果返回的字典只有一个，那么PyTuple_Size的返回值将会是-1，直接读取那个字典中的内容就可以。

```Python
def get_dict_data(name,age):
    n={'Name' : name,
        'Age' : int(age),
       }
    return n
```

```Python
def get_dict_data(name,age):
    r = {
        "Location" : "BeiJing",
        "Hobby" : "Music",
    }
    n = {'Name': name,
         'Age': int(age),
         }
    return r,n
```

```C++
#include <iostream>
#include <string>
#include "Python.h"
using namespace std;

//Python3中将PyObject转化为string
string converPyObjectToString(PyObject* obj)
{
	PyObject* repr = PyObject_Repr(obj);
	PyObject* strObj = PyUnicode_AsEncodedString(repr, "utf-8", "~E~");
	string str = PyBytes_AS_STRING(strObj);
	return str;
}

int Proc_PyDict(PyObject* pyValue)
{
	PyObject *key_dict = PyDict_Keys(pyValue);
	Py_ssize_t len = PyDict_Size(pyValue);
	for (Py_ssize_t i = 0; i<len; ++i)
	{
		PyObject* key = PyList_GetItem(key_dict, i);
		string str_key = converPyObjectToString(key);
		cout << str_key << ":\t";

		PyObject* value = PyDict_GetItem(pyValue, key);
		if (PyLong_Check(value)){
			int int_value = PyLong_AsLong(value);
			cout << "\t" << int_value << endl;
		}else{
			string str_value = converPyObjectToString(value);
			cout << "\t" << str_value << endl;
		}
		
	}
	return 0;
}

int main(int argc, char *argv[])
{
	//init python
	Py_Initialize();
	if (!Py_IsInitialized()){
		cout << "Init python failed !" << endl;
		return -1;
	}

	PyObject* pModule = PyImport_ImportModule("Dict");
	if (!pModule){
		cout << "Get python module failed !";
		return -1;
	}

	PyObject* pv = PyObject_GetAttrString(pModule, "get_dict_data");
	if (!pv || !PyCallable_Check(pv)){
		cout << "Can't find function !";
		return -1;
	}

	//pass parameters to python's interface and all it
	PyObject *args = Py_BuildValue("(si)", "Lily", 18);
	PyObject *pRet = PyObject_CallObject(pv, args);

	Py_ssize_t dict_size = PyTuple_Size(pRet);
	cout << "Directory size " << dict_size << endl;
	if (dict_size < 0){
		Proc_PyDict(pRet);
	}else{
		for (Py_ssize_t i = 0; i < dict_size; i++){
			PyObject* pyDict = PyTuple_GetItem(pRet, i);
			Proc_PyDict(pyDict);
		}
	}
	Py_Finalize();
	return 0;
}
```

##### 获取Python返回的列表

实现一个可以获取Python函数返回的一个或者多个列表，并依次获取的列表中的内容，并用下面两个python脚本进行测试。

```Python
import os,sys
def get_list_data():
    n = [1,2,3,4,5, "None","List"]
    return n
```

```Python
import os,sys
def get_list_data():
    r = ["First","Second","Third"]
    n = [1,2,3,4,5, "None","List"]
    return r,n
```

```C++
#include <iostream>
#include <string>
#include "Python.h"
using namespace std;

int main(int argc, char *argv[])
{
	Py_Initialize();
	if (!Py_IsInitialized()){
		cout << "Init python failed !" << endl;
		return -1;
	}

	PyObject* pModule = PyImport_ImportModule("Dict");
	if (!pModule){
		cout << "Get python module failed !";
		return -1;
	}

	PyObject* pv = PyObject_GetAttrString(pModule, "get_list_data");
	if (!pv || !PyCallable_Check(pv)){
		cout << "Can't find function !";
		return -1;
	}

	PyObject *pRet = PyObject_CallObject(pv, NULL);
	if (PyList_Check(pRet)){
		//just one list
		int list_len = PyList_Size(pRet);
		for (int i = 0; i < list_len; i++){
			PyObject* list = PyList_GetItem(pRet, i);
			if (PyLong_Check(list)){
				int int_list = PyLong_AsLong(list);
				cout << "list int     " << int_list << endl;
			}else{
				PyObject* repr = PyObject_Repr(list);
				PyObject* str = PyUnicode_AsEncodedString(repr, "utf-8", "~E~");
				string str_list = PyBytes_AS_STRING(str);
				cout << "list string  " << str_list << endl;
			}
		}
	}

	if (PyTuple_Check(pRet)){
		Py_ssize_t list_size = PyTuple_Size(pRet);
		cout << "list size " << list_size << endl;
		//more than one lists
		for (int k = 0; k < list_size; k++){
			PyObject* parm = PyTuple_GetItem(pRet, k);
			int list_len = PyList_Size(parm);
			for (int i = 0; i < list_len; i++){
				PyObject* list = PyList_GetItem(parm, i);
				if (PyLong_Check(list)){
					int int_list = PyLong_AsLong(list);
					cout << "list int     " << int_list << endl;
				}else{
					PyObject* repr = PyObject_Repr(list);
					PyObject* str = PyUnicode_AsEncodedString(repr, "utf-8", "~E~");
					string str_list = PyBytes_AS_STRING(str);
					cout << "list string  " << str_list << endl;
				}
			}
		}
	}

	Py_Finalize();
	return 0;
}
```









