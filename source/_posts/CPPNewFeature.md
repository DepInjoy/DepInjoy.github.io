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
- shared_ptr不能托管指向动态分配的数组指针，否则程序会出错。

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
	<A> sp1(a);
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

​	

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



### 强制类型转换



#### static_cast

​	用来进行比较低风险之间的转换，如整型和实数，整形和字符等等之间的转换。它不可以进行不同类型指针、不同类型之间引用，整数和指针之间转化。

```C++
#include <iostream>
class A {
public:
	operator int()	 { std::cout << "A::int " << std::endl;		return 8; }
	operator char*() { std::cout << "A::char* " << std::endl;	return NULL; }
};

void main(void)
{
	A a;
	char* c = "Hello";
	int t1 = static_cast<int>(a);		//A::int 
	char* c1 = static_cast<char*>(a);	//A::char* 
	char* c2 = static_cast<char*>(c);	//Hello
	std::cout << c2 << std::endl;
	//不允许将指针转化为int
    //int p = static_cast<int>(c1);
    //不允许将int转化为指针
	//char* c3 = static_cast<char*>(t1);
}
```



#### reinterpret_cast
```C++
#include <iostream>
class A {
public:
	int m;
	int n;
	A(int i) :m(i), n(i) {};
};
void main(void)
{
	A a(8);
    //使tmp引用a
	int &tmp = reinterpret_cast<int&>(a);
	tmp = 16;
	std::cout << a.m << " " << a.n << std::endl;	//16 8
}
```



#### const_cast

​	用来去除const属性。将const引用转化为非const引用，将const指针转化为非const指针。

```C++
#include <iostream>
void main(void)
{
	const std::string s = "Hello World";
	std::string &tmp0 = const_cast<std::string&>(s);
	std::cout << tmp0.data() << std::endl;						//Hello World
	tmp0 = "Next";
	std::cout << tmp0.data() << " " << s.data() << std::endl;	//Next Next
	std::string *tmp1 = const_cast<std::string*>(&s);
	std::cout << tmp1->data() << std::endl;						//Next
	system("pause");
}
```



#### dynamic_cast

​	专门用于将多态基类的指针或者引用转化为派生类指针或者引用，该转换很安全，如果不安全，则会返回为NULL。如果转化时不安全的，会抛出bad_cast异常。

```C++
#include <iostream>
class Base {};
class Derived : public Base {};
void main(void)
{
	Base b;
	Derived d;
	Base* tmp0 = dynamic_cast<Base*>(&d);
	if (!tmp0) std::cout << "0 dynamic cast sec " << std::endl;

	Derived* tmp1 = dynamic_cast<Derived*>(&d);
	if (!tmp1) std::cout << "1 dynamic cast failed " << std::endl;

	Base& tmp2 = dynamic_cast<Derived&>(d);
	if(!&tmp2) std::cout << "2 dynamic cast failed " << std::endl;
}
```



### 异常处理

```C++
#include <iostream>
double div(double a, double b)
{
	if (b == 0.0) {
		throw(-1);			//抛出异常
	}
	return a / b;
}
void main(void)
{
    //捕捉异常并进行处理
	try{
		div(1.0, 0.0);
	}catch (int e){
		std::cout << "not allowed " << e << std::endl;
	}
}
```



#### Lamda表达式

```C++
bool cmp(int a, int b){
	return  a < b;
}
sort(vec.begin(), vec.end(), cmp);
for (int it : vec)
		cout << it << ' ';
```

等价于：

```C++
	//利用Lamda表达式实现vec按照大小顺序进行排序
	sort(vec.begin(), vec.end(), [](int a, int b) -> bool { return a < b; });
	//利用Lamda进行vec遍历并打印
	for_each(lbvec.begin(), lbvec.end(), [](auto x){ cout << x << " "; });
```



实现斐波那契数列

```C++
#include <functional>
//std::function <int(int)>表示入参为int类型，返回值也为int
std::function <int(int)> flib = [&flib](int n) {
	return n <= 2 ? 1 : flib(n - 2) + flib(n - 1);
};
```

