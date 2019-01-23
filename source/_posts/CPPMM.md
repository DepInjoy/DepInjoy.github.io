---
layout: post
title: C++内存申请、释放以及管理
date: 2017-3-16 18:04
category: CPP
tags: [CPP]
description: 本文主要描述C++中内存的申请、释放的方法。
1、new和malloc的区别
2、static变量实现的机理。
---



### 动态内存申请

在实际的使用中，我们可以通过两种方式来申请内存：

- 调用malloc从系统申请制定大小的内存，如果成功则返回指向该由连续字节构成的内存块的指针。在不需要再使用时调用free函数将该内存空间释放。同时我们可以通过realloc来调整（增大或减少）内存空间的大小。

  ```C++
  #include <iostream>
  #include <stdlib.h>
  using namespace std;
  
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
  
  int main(int argc, char* argv[])
  {
  	char* tmp = new char[8];
  	cout << _msize(tmp) << endl;					//8
  	delete []tmp;
  	return 0;
  } 
  ```

上述两种申请内存的方式，由如下的区别：

- new和delete无需另外包含头文件；malloc需要添加```#include <stdlib.h>```头文件。
- new运算符返回正确类型，在对指针进行赋值时，无需进行类型转化。malloc的返回值是void*，在调用malloc时需要进行显示的类型强制转换成为所需要的指针类型。 
- malloc/free是C++/C标准库函数，new/delete是C++运算符。使用new运算符时自动调用该对象的构造函数，使用delete则自动调用析构函数。而malloc/free是库函数不是运算符，不再编译器控制权限内，无法自动调用构造/析构函数
- 如果希望改变分配内存的大小，只能通过C标准函数malloc、realloc和free来管理大小变化的堆内存块。不可以将new、delete与调整内存管理的函数混合使用，否则会在运行时导致内存管理的崩溃。
- new运算符可以重载，实现自定义内存分配策略，甚至于不分配内存，甚至分配到费内存设备上，而malloc则不可以。