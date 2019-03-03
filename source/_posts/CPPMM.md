---
layout: post
title: C++内存申请、释放以及管理
date: 2017-3-16 18:04
category: CPP
tags: [CPP]
description: 本文主要描述C++中内存的申请、释放的方法。
​	1、new和malloc的区别

​	2、static变量实现的机理。



### 动态内存申请

在实际的使用中，我们可以通过两种方式来申请内存：

- 调用malloc从系统申请制定大小的内存，如果成功则返回指向该由连续字节构成的内存块的指针，如果失败则返回NULL。在不需要再使用时调用free函数将该内存空间释放。同时我们可以通过realloc来调整（增大或减少）内存空间的大小。

  ```C++
  #include <iostream>
  #include <stdlib.h>
  using namespace std;
  /*
  	malloc和remalloc函数实现申请内存、调整（增大、减少）内存示例
  */
  int main(int argc, char* argv[])
  {
  	char* tmp = (char*)malloc(8 * sizeof(char));	//申请8个字节内存空间
  	cout << _msize(tmp) << endl;					//显示指针对应的内控空间大小，8
  	
      tmp = (char*)realloc(tmp, 16 * sizeof(char));	//将原申请的内存空间扩充到16个字节
  	cout << _msize(tmp) << endl;					//16
  
  	tmp = (char*)realloc(tmp, 6 * sizeof(char));	//将原申请的内存空间缩小到6个字节
  	cout << _msize(tmp) << endl;					//6
  	
      free(tmp);									//释放申请的内存空间
  	return 0;
  }
  ```

- 调用new在堆内为结构体分配一块内存空间，在不需要使用时，使用delete释放该内存空间。

  ```C++
  #include <iostream>
  using namespace std;
  /*
  	new运算符实现申请内存，remalloc函数实现调整（增大、减少）内存示例
  */
  int main(int argc, char* argv[])
  {
  	char* tmp = new char[8];
  	cout << _msize(tmp) << endl;					//8
  	
      tmp = (char*)realloc(tmp, 16 * sizeof(char));
  	cout << _msize(tmp) << endl;					//16
  
  	tmp = (char*)realloc(tmp, 6 * sizeof(char));
  	cout << _msize(tmp) << endl;					//6
      delete []tmp;
  	return 0;
  } 
  ```

上述两种申请内存的方式，由如下的区别：

- new和delete无需另外包含头文件；malloc需要添加```#include <stdlib.h>```头文件。
- new运算符返回正确类型，在对指针进行赋值时，无需进行类型转化。malloc的返回值是void*，在调用malloc时需要进行显示的类型强制转换成为所需要的指针类型。 
- malloc/free是C++/C标准库函数，new/delete是C++运算符。使用new运算符时自动调用该对象的构造函数，使用delete则自动调用析构函数。而malloc/free是库函数不是运算符，不再编译器控制权限内，无法自动调用构造/析构函数
- malloc需要用户传递欲分配内存的大小参数，而new则会根据传递参数自动计算需要分配的内存的大小，如果希望改变分配内存的大小，只能通过C标准函数malloc、realloc和free来管理大小变化的堆内存块。
- new运算符可以重载，实现自定义内存分配策略，甚至于不分配内存，甚至分配到费内存设备上，而malloc则不可以。

#### 重载new和delete运算符

​	new和delete属于C++中的运算符，我们可以实现对new和delete重载，实现手动管理内存(修改分配和归还工作)。operator new和operator delete只适用于单一对象的内存分配和归还，而Arrays使用的内存由operator new[]分配并由operator delete[]来进行归还。

常常出现下面的情况，我们可能需要对new/delete运算符重载：

- 堆空间已经耗尽，自动开始把某个磁盘文件当虚存储使用
- 用户希望控制某片存储空间的分配。



​	下面介绍一下new/delete运算符重载的实现。重载operator new和operator delete，还需要重载operator new[]和operator delete[]。首先我们来了解一些new/delete运算符的重载规则：

1. 重载operator new的参数个数可以是任意个数，但是第一个参数类型必须是size_t，返回值是void*.
2. 重载operator delete传入的参数类型只能是void。

相关实现的示例代码如下：

##### 局部重载new/delete

​	局部重载new/delete支持将new/delete重载成员函数和友元函数两种形式，相关实现代码如下：

###### 重载为成员函数

​	重载new运算符后，再用new给某个运算符分配空间，在结束调用后使用delete来释放内存空间，实际的调用顺序如下：

1.  new的重载函数
2. 类的构造函数
3. 类的析构函数
4. delete的重载函数

```C++
#include <iostream>
using namespace std;
class A
{
public:
	A();
	~A();
	void* operator new(size_t size);
	void operator delete(void* p);
	void* operator new[](size_t size);
	void operator delete[](void*p);
};

A::A()
{	
	cout << "Construct A " << endl;
}

A::~A()
{
	cout << "Deconstruct A " << endl;
}

void* A::operator new(size_t size)
{
	cout << "A::new " << size << " memmory " << endl;
	return malloc(size);
}

void* A::operator new[](size_t size)
{
	cout << "A:new[] " << size << " memmory " << endl;
	return operator new(size);
}

void A::operator delete(void* p)
{
	cout << "delete " << endl;
	free(p);
}

void A::operator delete[](void* p)
{
	cout << "delete[] " << endl;
	operator delete(p);
}
int main(int argc, char* argv[])
{
	/*
    	A::new 1 memmory 
    	Construct A 
    	Deconstruct A 
    	Free memmory
        ---------------------------------
        A:new[] 6 memmory 
        A::new 6 memmory
        Construct A
        Construct A
        Deconstruct A
        Deconstruct A
        delete[] 
        delete 
        ---------------------------------
    */
	A* tmp = new A();
	delete tmp;
	cout << "---------------------------------" << endl;
	
	A* tmp0 = new A[2];
	delete[] tmp0;
	cout << "---------------------------------" << endl;
	
    int* i = new int;
	delete i;
	return 0;
}
```

###### 重载为友元函数



##### 全局重载new/delete

​	全局重载new/delete后，C++原定义的new/delete将被忽略，重载实现将应用于所有的类型的内存分配。

```C++
#include <iostream>
using namespace std;
void* operator new(size_t size)
{
	if (!size)	return NULL;
	cout << "new " << size << " memory !" << endl;
	return malloc(size);
}
void operator delete(void *p)
{
	cout << "delete " << endl;
	free(p);
}

void* operator new[](size_t size)
{
	if (!size)	return NULL;
	cout << "new[] " << size << " memory !" << endl;
	return operator new(size);
}
void operator delete[](void* p)
{
	cout << "delete[]" << endl;
	operator delete(p);
}
int main(int argc, char* argv[])
{
	/*
		new 4 memory !
		delete
		---------------------
		new[] 12 memory !
		new 12 memory !
		delete[]
		delete 
	*/
	int* tmp = new int;
	delete tmp;
	cout << "---------------------" << endl;
	int* tmp0 = new int[3];
	delete[] tmp0;
	return 0;
}
```
