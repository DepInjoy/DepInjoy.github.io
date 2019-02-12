---
layout: post
title: 运算符重载
date: 2018-03-25 20:00
category: CPP
tags: [CPP]
description: 运算符重载可以提高C++提供的运算符的适用范围，使其可以作用于对象。本文给出了=、-、>>、<<、++、类型转换运算符重载的示例以及运算符重载为友元，可以加深对运算符重载的理解。并结合郭炜老师的字符串的深浅拷贝的实践，加深对运算符重载返回值和复制运算符操作的理解和实践中需要注意的事项。
---

运算符的重载实质上是函数的重载，运算符的可以被多次重载，根据传入参数的类型来决定调用哪个运算符函数。表达形式：

```c++
返回值类型 operator 运算符(形参列表)
```

- 只可以重载已有的运算符，不可以发明新的运算符。
- 重载时，参数的数量和该运算符数目一致。如果重载为成员函数(此时，它的第一个运算对象自动绑定至隐式this指针，参看示例的重载-运算符)，则参数个数减一，而对于普通的函数，参数个数为运算符数目(参看示例的重载-运算符)。
- 赋值运算符=只能重载为成员函数(参看示例=运算符)
- 重载运算符的优先级和结合律需要与内置运算符保持一致。
- 以下运算符不允许重载.，".*"、“::”、“？：“、”sizeof“。

```c++
#include <iostream>
class Complex{
public:
	Complex(double r = 0.0, double i = 0.0);
	Complex operator-(const Complex& c);
	Complex& operator=(const Complex& c);
	double real;
	double imag;
};
Complex::Complex(double r, double i){
	real = r;
	imag = i;
}

Complex Complex::operator-(const Complex& c){
	return Complex(real - c.real, imag - c.imag);
}

Complex operator+(const Complex& a, const Complex& b){
	return Complex(a.real + b.real, a.imag + b.imag);
}

Complex& Complex::operator= (const Complex& c){
	real = c.real;
	imag = c.imag;
	return *this;
}

int main(int argc, char* argv[]){
	Complex a(8, 8), b(6, 6);
	Complex c = a + b;
	std::cout << "real: " << c.real << " image: " << c.imag << std::endl;  //14,14
	c = a - b;
	std::cout << "real: " << c.real << " image: " << c.imag << std::endl; //2,2
	c = a;
	std::cout << "real: " << c.real << " image: " << c.imag << std::endl; //8,8
	system("pause");
	return 0;
}
```

## 运算符重载为友元函数

由于普通函数无法访问私有成员，我们可以将运算符重载为友元来解决该问题。首先看下面的这段代码：

```C++
#include <iostream>
class Complex{
public:
	Complex(double r = 0.0, double i = 0.0);
	Complex operator+(double r);
	double realV(void)	{ return real; }
	double imagV(void)	{ return imag; }
private:
	double real;
	double imag;
};
Complex::Complex(double r, double i){
	real = r;
	imag = i;
}
Complex Complex::operator+(double r)
{
	return Complex(real + r, imag);
}

int main(int agrc, char* argv[]){
	Complex c1(6, 8);
	c1 = c1 + 5;
	std::cout << "c1.real = " << c1.realV() << " c1.image = " << c1.imagV() << std::endl;	//11,8
	system("pause");
	return 0;
}
```

上述的代码我们可以成功执行：c1 = c1 + 5(等价于c1=c1.operator+(5)),但是却无法c1=5+c1却无法正常运行。此时我们可以添加全局函数Complex operator+(double r, const Complex& c);来确保正常运行，却无法访问Complex的私有成员real和imag，此时我们可以将该函数声明为友元来解决该问题。

```C++
friend Complex operator+(double r, const Complex& c);
```

```C++
Complex operator+(double r, const Complex& c)
{
	return Complex(r + c.real, c.imag);
}
```

执行下面的测试代码，成功：

```C++
	c1 = 5 + c1;
	std::cout << "c1.real = " << c1.realV() << " c1.image = " << c1.imagV() << std::endl;//16,8
```

### 运算符重载之深入探讨

看到了清华大学郭炜老师一个很有意思的关于运算符重载关于深浅拷贝的探讨，在这里将相关的过程进行一一的实验总结，首先我们查看下面实现=运算符重载的代码：

```C++
#include <string>
class String{
public:
	char * str;
	String() :str(new char[1]){ str[0] = 0; }
	String& operator=(const char *s);
	~String(){ delete[]str; }
};
String& String::operator=(const char* s)
{
	delete[] str;
	str = new char[strlen(s)+ 1];
	strcpy_s(str, strlen(s) + 1, s);
	return *this;
}
int main(int argc, char* argv[])
{
	String s1,s2;
    //String s1 = "Hello";	//编译无法通过，此时是类的构造，调用的是String(char*)的构造函数		s1 = "Hello";			//s1.operator=("Hello")
	std::cout << "s1.str = " << s1.str << std::endl;//Hello
	s1 = "World";
	std::cout << "s1.str = " << s1.str << std::endl; //World
	return 0;
}
```

上面的代码可以正常运行，存在一个问题如果通过

```c++
	s2 = s1;
	std::cout << "s2.str = " << s2.str << std::endl;
```

直接进行复制重载，默认的=运算符重载函数会将s2.str也将指向s1.str的地址，那么在析构的时候会出现两次释放内存而出现问题。我们可以再构造一个=运算符重载的成员函数：

```C++
String& String::operator=(const String& s)
{
	delete[] str;
	str = new char[strlen(s.str) + 1];
	strcpy_s(str, strlen(s.str) + 1, s.str);
	return *this;
}
```

这样可以避免上面地址重复释放的问题，但是如果执行下面的操作会引入新的问题

```C++
	s1 = s1;
	std::cout << "s1.str = " << s1.str << std::endl;
```

在赋值运算符重载的函数中，我们在刚开始执行便执行 delete[] str; 将str的内存释放掉，而在接下来我们却要访问s.tr，而在s1=s1(有可能出现x = s1, s1 = x)的操作中，s指向this，这样自然会引入新的问题。我们可以在函数的开头来避免s1=s1的后续执行，来避免上述问题：

```C++
	if (this == &s)
		return *this;
```

之后过郭炜老师又探讨了，operator=返回值的问题，=运算符重载返回值存在void、String、String&等情况。而在对运算符重载过程中，我们希望尽可能保持运算符原有的特性，那么对=考虑下面两种情况：

- a=b=c，等价于a.operator=(b.operator=(c))。(void返回值不合理，排除)

- (a=b)=c，等价于(a.operator=(b)).operator=(c)。在C++中=运算符的返回值是等号左边的引用，a=b的返回值是a的引用。返回值是String&则此次修改只是修改a的值，和b的值无关。


## 可变长度的数组类实现

[参看代码](https://github.com/DepInjoy/BaseHouse/blob/master/CPP/VariableLengthArray.cpp)实现了对=、[]运算符的重载、push_back在尾部追加数据等等一系列的操作。其中在对push_back实现时可以一次性分配较大的内存，用空间来换区时间，相关实现如下：

```C++
	if (!ptr){
		ptr = new int[4];
	}else{
		if (size % 4 == 0){
			int* tmp = new int[4 * (size / 4 + 1)];
			memcpy(tmp, ptr, size * sizeof(int));
			delete ptr;
			ptr = tmp;
		}
	}
```

## 输入输出运算符重载

[参看代码](https://github.com/DepInjoy/BaseHouse/blob/master/CPP/inAndOutOverloading.cpp)实现了对>>和<<运算符的重载。为了保持运输入运算符(>>)可以继续输入特性函数的返回值为std::istream&，同样的输出运算符重载<<的返回值为std::ostream&,而给函数想要访问Complex的私有成员，所以将函数定义为友元。

## 重载类型转换运算符

```C++
#include <iostream>
class  Complex
{
public:
	Complex(double r = 0.0, double i = 0.0){ real = r; image = i; }
	operator double() { return real; }
private:
	double real;
	double image;
};

int main(int argc, char* argv[])
{
	Complex c(3.14, 6.18);
	double tmp = (double)c;
	std::cout << tmp << std::endl;		//3.14
	double tmp2 = c + 6;			//double tmp2 = c.operator.double() + 6
	std::cout << tmp2 << std::endl;		//9.14
	system("pause");
	return 0;
}
```



## 自增自减运算符重载

关于自增(减)运算符的前置和后置，C++有如下定义：

- 前置运算符重载为一元运算符，重载为成员函数。为保持与内置版本运算符的一致性，函数的返回值应该是成员的引用。
  - 重载为成员函数，T& operator++(); T& operator--();
  - 重载为全局函数，T1& operator++(T2); T1& operator--(T2);
- 后置运算符重载为二元运算符，多传递一个无用的参数。为保持与内置版本运算符的一致性，函数的返回值应该是成员的原值。
  - 重载为成员函数，T& operator++(int); T& operator--(int);
  - 重载为全局函数，T1& operator++(T2， int); T1& operator--(T2， int);

《C++ primer》建议将自增自减运算符重载为成员函数，且同时定义前置和后置版本。在后置运算符要对对象进行复制，会增大运算开销，所以在++T和T++等价的情况下，优先使用++T来减少时间开销。

```C++
#include <iostream>
class  Complex
{
public:
	Complex(double r = 0.0, double i = 0.0){ real = r; image = i; }
	Complex& operator++();							//前置，成员函数重载
	Complex operator++(int);						//后置，成员函数重载
	friend Complex& operator--(Complex& c);			//前置，全局函数重载
	friend Complex operator--(Complex& c, int);		//后置，全局函数重载
	double realValue()		{ return real; }
	double imageValue()		{ return image; }
private:
	double real;
	double image;
};

Complex& Complex::operator++()
{
	++real;
	++image;
	return *this;
}
Complex Complex::operator++(int)
{
	Complex tmp(*this);
	real++;
	return tmp;
}
Complex& operator--(Complex& c)
{
	c.real--;
	c.image--;
	return c;
}
Complex operator--(Complex& c, int)
{
	Complex tmp(c);
	c.real--;
	return tmp;
}

int main(int argc, char* argv[])
{
	Complex c(6, 8);
	std::cout << "c     real = " << c.realValue() << " image = " << c.imageValue() << std::endl;	// 6,8
	c++;				//c.operator++(0)
	std::cout << "c++   real = " << c.realValue() << " image = " << c.imageValue() << std::endl;	// 7,8
	++c;                //c.operator++()
	std::cout << "++c   real = " << c.realValue() << " image = " << c.imageValue() << std::endl;	// 8,9
	c--;				//operator--(c,0)
	std::cout << "c--   real = " << c.realValue() << " image = " << c.imageValue() << std::endl;	// 7,9
	--c;				//operator--(c)
	std::cout << "--c   real = " << c.realValue() << " image = " << c.imageValue() << std::endl;	// 6,8
	system("pause");
	return 0;
}
```

