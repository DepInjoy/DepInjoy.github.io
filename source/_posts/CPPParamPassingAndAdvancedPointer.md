---
layout: post
title: 参数传递和高级指针
date: 2017-3-18 18:04
category: CPP
tags: [CPP、C]
description: 本文通过实例的形式展示了指针和二级指针(指针的指针)间操作的不同，在函数内部，二级指针可以更改指针指向的地址而一级指针只能更改指针指向对象的值。同时介绍了函数引用参数传递和值传递。
---



### 高级指针

#### 二级指针

​	二级指针指向的是指针的指针，即地址的地址。

```C++
#include <stdlib.h>
#include <stdio.h>

int main(int argc,char *argv[])
{
    int m = 18,n = 28;
    int *mp = &m;
    printf("mp %d %p\tm %d %p\n",*mp,mp,m,&m);
    mp = &n;
    printf("mp %d %p\tm %d %p\tn %d %p\n\n",*mp,mp,m,&m,n,&n);

    int i = 6,j = 8;
    int *ip1 = &i,*ip2 = &j;
    printf("ip1 %d %p\tip2 %d %p\n",*ip1,ip1,*ip2,ip2);
    int **ipip = &ip1;
    printf("ip1 %d %p\tip2 %d %p\tipip %d %p\n",**ipip,*ipip,*ip1,ip1,*ip2,ip2);
    *ipip = ip2;
    printf("ip1 %d %p\tip2 %d %p\tipip %d %p\n",**ipip,*ipip,*ip1,ip1,*ip2,ip2);
    return 0;
}
```

执行的结果如下：

```
mp 18 000000000029FC64	m 18 000000000029FC64
mp 28 000000000029FC84	m 18 000000000029FC64	n 28 000000000029FC84

ip1 6 000000000029FCA4	ip2 8 000000000029FCC4
ip1 6 000000000029FCA4	ip2 6 000000000029FCA4	ipip 8 000000000029FCC4
ip1 8 000000000029FCC4	ip2 8 000000000029FCC4	ipip 8 000000000029FCC4
```

从上面的示例可以看到：指针操作只是改变本变量的地址进而使地址中存放的对象改变，而指针的指针的对象操作，导致改变原指向变量的地址和本变量地址，进而导致相应的内容的改变。



### 参数传递

​	一个函数的定义包含四个部分：返回值类型，函数名字以及由0到多个形参组成的列表，函数体。我们通过调用运算符(圆括号)来作用于表达式进而执行函数。而**函数的调用主要完成两项工作：一是用实参来初始化形参；二是将控制权转移给被调函数**，此时主调函数执行暂时中断，被调函数开始执行。

​	参数传递包含值传递和引用传递，**形参的类型决定了形参和实参的交互方式，如果形参是引用，则它将绑定到相应的形参，否则实参值拷贝后赋值给形参。**



#### 传值参数

​	当初始化一个非引用类型变量时，初始值拷贝给变量，此时对变量的改动不会影响初始值。形参形式包含如下几种：

##### 指针形参

​	指针形参和其他的非引用类型一样需要进行变量拷贝。当执行指针拷贝时，拷贝的是指针的值，拷贝之后两个指针指向的是不同的指针，通过指针我们可以间接访问它做指向的对象，进而修改它的值。

```C++
#include <iostream>
#include <iomanip>

int a = 0;
int b = 18;
int *p = &a;

//格式化输出结果显示，resetiosflags()清除显示格式
void formatPrint(int a, int b, int* c){
	std::cout << "-------------------------------------------------" << std::endl;
	std::cout << std::setiosflags(std::ios::left) << std::setw(24) 
        << "Address" << resetiosflags(std::ios::left) 
        << std::setiosflags(std::ios::right) << std::setw(4) 
        << "Value" << std::resetiosflags(std::ios::right) << std::endl;
	std::cout << std::setiosflags(std::ios::left) << std::setw(24)
        << &a << resetiosflags(std::ios::left) 
        << std::setiosflags(std::ios::right) 
        << std::setw(4) << a << std::resetiosflags(std::ios::right) << std::endl;
	std::cout << std::setiosflags(std::ios::left) << std::setw(24)
        << &b << resetiosflags(std::ios::left) 
        << std::setiosflags(std::ios::right) 
        << std::setw(4) << b << std::resetiosflags(std::ios::right) << std::endl;
	std::cout << std::setiosflags(std::ios::left) 
        << std::setw(24) << p << resetiosflags(std::ios::left) 
        << std::setiosflags(std::ios::right) << std::setw(4) 
        << *p << std::resetiosflags(std::ios::right) << std::endl;
	std::cout << "-------------------------------------------------" << std::endl;
}
int main(int argc, char* argv[]){
	formatPrint(a, b, p);
	return 0;
}
```

此时，显示a、b、p对应的指针和参数值显示如下：

```shell
-------------------------------------------------
Address                 Value
000000013F250390           0
000000013F250000          18
000000013F250390           0
-------------------------------------------------
```



###### 一级指针

​	**一级指针可以实现修改指针存放的对象的值，却无法修改指针所指向的地址。**这是传入的参数是费引用，函数执行时，会将函数的实参拷贝给形参，这两个是相互独立的变量，但是我们是可以通过指针来简介修改指针锁指向的内容。由于相关的实现示例如下：

```C++
//尝试修改函数传进来的指针指向的地址
void reset(int *p){
	p = &b;
}
```

​	进行如下调用：

````CPP
reset(p);
formatPrint(a, b, p);
````

​	而此时，显示a、b、p对应的指针和参数值显示如下，由结果可见修改失败：

```shell
-------------------------------------------------
Address                 Value
000000013F250390           0
000000013F250000          18
000000013F250390           0
-------------------------------------------------
```

​	通过**函数的形参来修改传入的一级指针的地址失败了，但是我们是可以通过指针来修改该指针存放的内容**，相关的实现代码如下:

```C++
//尝试修改指针所指向的对象的值，指针地址不变，内容被修改
void reset(int *p){
	*p = b;
}
```

​	进行如下调用：

```C++
reset(p);
formatPrint(a, b, p);
```

​	而此时，显示a、b、p对应的指针和参数值显示如下：

```shell
-------------------------------------------------
Address                 Value
000000013F250390           0
000000013F250000          18
000000013F250390          18
-------------------------------------------------
```



###### 二级指针

​	二级指针可以实现：在函数外部定义一个指针p，在函数内对该指针赋值，函数结束后对指针p的修改生效。

```C++
//尝试修改指针地址
void reset(int **p)
{
	*p = &b;
}
```

进行如下调用：

```C++
reset(&p);
formatPrint(a, b, p);
```

而此时，显示a、b、p对应的指针和参数值显示如下：

```shell
-------------------------------------------------
Address                 Value
000000013F250390           0
000000013F250000          18
000000013F250000          18
-------------------------------------------------
```



#### 数组形参

​	**不允许拷贝数组（无法以值传递方式使用）以及使用数组时通常将其转化为指针（实际上传递的是指向数组首元素的指针）**的两个特殊性质，使得定义和使用作用在数组上的函数有影响。

```C++
//下面的三种形式是等价的
void print(const int *);
void print(const int[]);
void print(const int[10]);	//数组维度代表期望包含的元素数，实际上未必
```

​	管理指针形参有三种形式：

- 使用标记指定数组长度
- 使用标准规范，传递指向数组首元素和尾元素的指针。
- 显示传递一个表示数组大小的形参



#### 多维数组形参

​	实际上并没有多维数组，实际上的多维数组就是数组的数组。将多维数组作为参数传递给函数，实际上传递的是数组首元素的指针。

```C++
//10个指针构成的数组
int *matrix[10]
//含有10个整数数组的指针
int (*matrix)[10];
```

多维数组作为参数传递的形式：

```C++
void print(int (*matrix)[10], int rowSize);
void print(int  matrix[][10], int rowSize);
```



#### 引用参数

​	使用引用可以避免拷贝。拷贝大的类型对象或容器对象比较低效（建议使用引用形参），甚至某些类型并不支持拷贝(只能使用引用形参)。同时，如果无须改变引用形参的值，最好将其声明为常量引用。

​	下面是面试可能遇到的对传值和传引用形参的理解。

```C++
#include <iostream>
#include <iomanip>

class MString
{
public:
	MString();
	~MString();
	MString(MString other);
private:
	char *str;
	size_t size;
};

MString::MString()
	: str(new char[1])
	, size(1){
	str[0] = '\0';
}

MString::~MString(){
	delete[]str;
}

int main(int argc, char* argv[]){
	MString a;
	MString b = a;
	return 0;
}
```

​	首先上面的代码在VS中编译将会出现编译错误(```错误1 error C2652: “MString”: 非法的复制构造函数: 第一个参数不应是“MString”```)。出现该问题原因是：上面的赋值构造函数```MString(MString other);```传入的参数是```MString other```,这是传值参数，将实参初始化给形参调用赋值构造函数，如果赋值构造函数传值，就会出现赋值构造函数调用赋值构造函数，进而形成无休止的递归调用进而导致栈溢出。而引用调用是将实参绑定给相应的形参，可以避免拷贝，所以上述的复制构造函数可以定义为：

```
MString(MString& other);
```

或者常量引用，此时我们不可以修改引用形参的值。

```C++
MString(const MString other);
```



#### 数组引用形参

```C++
//引用的数组
void func(int &arr[10])
//具有10个整数的整型数组的引用
void func(int (&arr)[10])
```

```C++
//形参是数组的引用，维度是类型的一部分
void print(int(&arr)[10]){
	for (auto elem : arr){
		std::cout << elem << std::endl;
	}
}
```

调用形式：

```C++
//int k[2] = { 0, 1 };						//出错
int k[10] = { 0, 1, 2, 3, 4, 5, 6, 7, 8, 9 };
print(k);
```



##### 非类型模板参数

​	上面的实现，限制了传入数组的维度，实际上只要我们访问数组元素不超过维度就可以，非类型参数允许我们传递任意大小的数据。非类型参数表示一个值而非一个类型，通过class或者typename来指定一个非类型参数。需要注意的是：**当一个模板被实例化时，非类型参数被用户提供的或编译器推断出的值替代时，这些值必须是常量表达式，从而允许编译器在编译时实例化模板。**

```C++
template<typename T, unsigned N>
void print(T (&t)[N]){
	for (auto elem : t){
		std::cout << elem << std::endl;
	}
}
```

当然上述的非类型参数也可以指定为特定的类型，相关实现如下：

```C++
template< unsigned N>
void print(int(&t)[N]){
	for (auto elem : t){
		std::cout << elem << std::endl;
	}
}
```

调用形式：

```
int j[2] = { 0, 1 };
int k[10] = { 0, 1, 2, 3, 4, 5, 6, 7, 8, 9 };
print(k);
print(j);
```



#### 可变形参

##### 省略号传递可变实参

​	省略号形式仅适用于C和C++通用的类型，大多数类型对象再传递给圣罗浩形参时都无法正确拷贝。

```C++
#include <iostream>
#include <stdarg.h>

void print(int a, ...)
{
	va_list args;
	printf("%d ", a);
    //a作为固定参数，用来确定变参的起始地址
	va_start(args, a);
	printf("%d ", va_arg(args, int));
    //此处double更改为float会出错。
	printf("%.2f ", va_arg(args, double));
	printf("%c ", va_arg(args, char));
	va_end(args);
}

int main(int argc, char* argv[]){
	print(1,2,3.14,'a');			//1 2 3.14 a 
	return 0;
}
```



##### 新标准：实参类型相同，传递initializer_list标准库类型

```C++
void print(std::initializer_list<std::string> list)
{
	for (auto val = list.begin(); val != list.end(); val++)
		std::cout << val << std::endl;
}
```



##### 新标准：实参类型相同，编写可变参数模板

​	下面给出一个以递归的方式实现的可变参数模板，更多使用方式，可以参考《C++ Primer》P618。

```C++
template<typename T>
void print(std::ostream& os, const T& t){
	os << t << ", ";
}

template<typename T, typename ... Args>
void print(std::ostream& os, const T& t, const Args& ... args){
	os << t << ", ";
	print(os, args...);
}
```

```C++
//获取参数数目
std::cout << "sizeof...(Args) = "<< sizeof...(Args) << std::endl;
//获取函数参数数目
std::cout << "sizeof...(args) = "<< sizeof...(args) << std::endl;
```

使用方式如下：

```C++
int a = 6;
int b = 18;
std::string c = "Hello";
float d = 3.14;
print(std::cout, a, b, c, d);			//6, 18, Hello, 3.14
```

