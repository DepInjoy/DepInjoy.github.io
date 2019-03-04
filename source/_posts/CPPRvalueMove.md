---
layout: post
title: C++11新特性--右值引用和转义语义
date: 2019-3-4 18:04
category: CPP
tags: [CPP]
description: 
---

​	右值引用是C++11引入的新特性，它完美地解决了C++长期以来被人诟病的处理临时对象的效率问题。它实现了转移语义(Move Sementics)和精确传输(Perfect Forwarding)。它的主要目的是：

- 较少不必要的对象深拷贝，节省运算存储资源，提高程序运行的效率。
- 更加简洁地定义反省函数。



### 右值和左值简介

​        在C/C++中一个表达式要么是左值，要么是右值。一般来说，可以取地址的表达式就是左值，而无法取地址的表达式就是右值。如

```C++
// i是左值，0是右值
int i = 0;
// 定义右值b
int && b = 1;
```

​	利用左值和右值重载两个函数：

```C++
#include <iostream>
void process_value(int& i) {
	std::cout << "LValue processed: " << i << std::endl;
}

void process_value(int&& i) {
	std::cout << "RValue processed: " << i << std::endl;
}

int main() {
	int a = 0;
	process_value(a);		//LValue processed: 0
	process_value(2);		//RValue processed: 2
}
```

​        通过右值参数传入一个函数，该函数将该右值参数传递给另外一个参数时，就会变成左值。这是由于该参数在传递的过程中变成了命名对象。在上面的示例代码中追加下面的函数实现：

```C++
void forward_value(int&& i) { 
	process_value(i); 
} 
```

之后，右值调用该函数，

```C++
int&& b = 1;
process_value(b);			//LValue processed: 1
forward_value(2);			//LValue processed: 2
```

### 转移语义

​	右值支持转移语义，转移语义将资源（如堆等）从一个对象转移到另外一个对象，从而减少一些临时对象资源的申请、复制和销毁，从而提高程序的性能。在C++中我们可以定义复制构造函数和赋值函数来实现资源的复制，特殊地我们可以定义转移构造函数和转移赋值构造函数来实现右值拷贝，**如果没有定义转移构造函数和转移赋值函数，C++会默认调用复制构造函数和赋值函数。**

```C++
#include <iostream>
#include <sstream>
#include <string>
using namespace std;

class String
{
public:
	char* str;
	String();
	String(const char* s);
	String(const String& s);
	String& operator=(const String& s);

	~String() { delete []str; }
};
String::String()
{
	str = new char[1];
	str[0] = 0;
}
String::String(const char* s)
{
	str = new char[strlen(s) + 1];
	strcpy_s(str, strlen(s) + 1, s);
}
String::String(const String& s)
{
	cout << "copy constructor called !" << endl;
	str = new char[strlen(s.str) + 1];
	strcpy_s(str, strlen(s.str) + 1, s.str);
}
String& String::operator=(const String& s)
{
	cout << "= called !" << endl;
	if (str != s.str) {
		delete []str;
		str = new char[strlen(s.str) + 1];
		strcpy_s(str, strlen(s.str) + 1, s.str);
	}
	return *this;
}

int main(int argc, char* argv[])
{
	String s;
	s = String("Hello");						//= called !
	String &&s1 = String("World");
	return 0;
}
```

​	上面的程序中，```String("Hello")```和```String("World")```都是右值，而程序中的复制构造函数和赋值函数，却需要释放内存，重新申请内存并完成数据的复制，进行了一系列无疑的内存申请和释放操作。而转移语义直接将临时对象的资源转移过来，从而实现节约释放和申请资源的时间成本，从而提高程序运行的效率。定义抓你复制构造函数有如下需要注意的地方：

- 右值参数须添加右值引用符号(&&)。
- 由于右值参数会被修改，右值参数不可是常量。
- 右值参数的资源和标记必须修改，否则右值的西沟函数释放资源，导致新对象的资源失效。

给接下来我们添加转移复制构造函数：

```C++
	String(String&& s);
	String& operator=(String&& s);
```

```C++
String::String(String&& s) :str(s.str)
{
	cout << "move constructor " << endl;
	str = new char[1];
	str[0] = 0;
}
String& String::operator=(String&& s)
{
	cout << "move = called " << endl;
	if (str != s.str) {
		delete str;
		str = s.str;
		s.str = new char[1];
		s.str[0] = 0;
	}
	return *this;
}
```

再执行上面的程序：

```C++
	String s;
	s = String("Hello");						//move = called 
	String &&s1 = String("World");
```

std::move可以将一个左值转化为右值，右值运算可以很明显提高swp的运行效率.

```C++
template<class T>
void Tswap(T &t1, T &t2)
{
	T tmp(move(t1));		//move constructor 
	t1 = move(t2);			//move = called 
	t2 = move(tmp);			//move = called 
}
```

测试：

```C++
	//此操作后b为0，那么Tswap实际上只需要执行t1 = move(t2);即可达到相同的效果。
	String a("Nice"), sb(" to meet you!");
	Tswap(a, b);
	cout << a.str << endl;						// to meet you!
```



### 精确传递

一个参数具有如下的属性：

- 左值/右值
- const/非const。

而精确传递意味着这些属性都不可以被改变，这在泛型编程中很常见。这就意味着在泛型编程中我们需要重载如下T& 和 const T&两种函数：

```C++
template <class T> 
void func(const T& val) 

template <class T>
void func(T& val)
```

C++11 中定义的 T&& 的推导规则为：**右值实参为右值引用，左值实参仍然为左值引用。也就是参数的属性不改变**

```C++
#include <iostream>
void process_value(int& i) {
	std::cout << "LValue processed: " << i << std::endl;
}

void process_value(const int& i) {
	std::cout << "const LValue processed: " << i << std::endl;
}

void process_value(int&& i) {
	std::cout << "RValue processed: " << i << std::endl;
}

template <typename T> 
void forward_value(T&& val) {
	process_value(val);
}

int main() {
	int a = 0;
	const int &b = 1;
	forward_value(a);		//LValue processed: 0
	forward_value(b);		//const LValue processed: 1
	forward_value(2);		//LValue processed: 2
}
```

疑问：

```C++
forward_value(2);		//LValue processed: 2
```

应该是：

```C++
forward_value(2);		//RValue processed: 2
```



