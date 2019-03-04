---
layout: post
title: C++11新特性
date: 2018-3-27 18:04
category: CPP
tags: [CPP]
description: 
---

#### 统一的初始化方式

#### decltype

​	用于查询表达式的类型。

```C++
class A{
public:
	int age;
};

const A* a = new A();
decltype(a)				//A*
decltype(a->age)		//int
decltype((a->age))		//int&
```



#### auto关键字

​	在C++11中，auto变成了一种占位符，它通知编译器根据参数初始化的形式去判断变量的类型，在各种作用域中均可以用到，尤其在STL中迭代器的声明中，可以省去typedef的代码，进而写出更加简洁的代码。

```C++
auto i = 42;        // int类型
auto l = 42LL;      // long long类型

//auto省去了STL中迭代器声明，可以写出更加简洁的代码
//实际上it对应的数据类型是map<string, int >::iterator
map<string, int > tmp;
for (auto it = begin(tmp); it != end(tmp); ++it){
}
```

```C++
#include <iostream>
using namespace std;
class A{
public:
	A(int i){ n = i; }
	int n;
};

A operator+(int a, A& v){
	return A(v.n + a);
}

template<class T1, class T2>
auto add(T1 t1, T2 t2)->decltype(t1 + t2){
	return t1 + t2;
}

int main()
{
	auto tmp = add(2, 3.8);					//类型float
	auto tmp0 = add(18, A(16));				//类型A
	cout << tmp << " " << tmp0.n << endl;	//5.8 34
	return 0;
}
```



#### 成员变量设置默认初始值

```C++
#include <iostream>
#include <functional>
using namespace std;
class A{
public:
	int m = 8;
};
int main()
{
	A a;
	cout << a.m << endl;		//8
	return 0;
}
```



#### 智能指针shared_ptr

​	shared_ptr对象托管一个new运算符返回的指针，之后无须关心内存释放问题。

- 当多个shared_ptr对象托管一个指针时，系统会维护一个计数器，当无shared_ptr托管该指针时，便会delete该指针.
- shared_ptr不能托管指向动态分配的数组指针，否则程序娙会出错。

```C++
#include <memory>
#include <iostream>
using namespace std;
class A
{
public:
	int n = 0;
	A(int a){ n = a; };
	~A(){ cout << "Destruct A" << endl; };
};

int main(int argc, char* agv[])
{
	//sp1, sp2, sp3都托管A(6)
	shared_ptr<A> sp1(new A(6));	
	shared_ptr<A> sp2(sp1);
	shared_ptr<A> sp3 = sp2;
	cout << sp1->n << " " << sp2->n << " " << sp3->n << endl;		//6 6 6
	A* tmp = sp1.get();
	cout << tmp->n << endl;

	sp1.reset();		//sp1放弃托管A(6)
	if (!sp1) cout << "sp1 = NULL" << endl;

	A* tmp2 = new A(8);
	sp1.reset(tmp2);	//sp1托管A(8)
	cout << sp1->n << endl;										//8
	return 0;
}
```

**测试用例一：**

```C++
上面的程序在程序终止时，自动调用A的析构函数
```

**测试用例二**

```C++
//sp2、sp3都放弃托管，此时指针无托管，调用析构，释放资源
//程序打印出一个Destruct A，疑问为什么不是打印出两个？
sp2.reset();
sp3.reset();
```


```C++
	A * a = new A(6);
	shared_ptr<A> sp1(a);
	//该操作并不增加shared_ptr对指针的托管，程序由于两次释放A导致crash
	shared_ptr<A> sp2;
	sp2.reset(a);
```



#### 空指针nullptr

​	nullptr和任何指针类型以及类成员指针类型的空值之间发生隐式类型转换，同样也可以隐式转换为bool（取值为false），但是不存在到整形的隐式类型转换。

```C++
int* p1 = NULL;
int* p2 = nullptr;   
if(p1 == p2){}

bool f = nullptr;	// false
int i = nullptr; 	// nullptr不可以转化为整形
```



### 右值引用和move语义

​	右值引用（及其支持的Move语意和完美转发）是C++0x加入的最重大语言特性之一。从实践角度讲，它能够完美解决C++中长久以来为人所诟病的临时对象效率问题。从语言本身讲，它健全了C++中的引用类型在左值右值方面的缺陷。从库设计者的角度讲，它给库设计者又带来了一把利器。从库使用者的角度讲，不动一兵一卒便可以获得“免费的”效率提升… 

​	一般来说无法取地址的表达式就是右值，而可以取地址的表达式是左值。它的主要目的是提高程序运行效率，减少需要进行深浅拷贝对象的深浅拷贝次数。

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

	String(String&& s);
	String& operator=(String&& s);

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

template<class T>
void Tswap(T &t1, T &t2)
{
	T tmp(move(t1));
	t1 = move(t2);
	t2 = move(tmp);
}
int main(int argc, char* argv[])
{
	String s;
	s = String("Hello");
	cout << "----------------------------" << endl;
	String &&s1 = String("World");

	String s0("Nice"), s00(" to meet you!");
	Tswap(s0, s00);
	cout << s0.str << endl;
	cout << s00.str << endl;

	system("pause");
	return 0;
}
```



### 基于范围的for循环

```C++
#include <memory>
#include <vector>
#include <iostream>
using namespace std;

int main(int argc, char* agv[])
{
	vector<int> v{ 0, 1, 2, 3, 4, 5 };
	for (auto& it : v){
		cout << it << endl;
	}

	int array[6]{0, 1, 2, 3, 4, 5};
	for (int v : array){
		cout << v << endl;
	}
	return 0;
}

```

