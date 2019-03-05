---
layout: post
title: 构造、析构和复制构造函数
date: 2018-03-23 17:24
category: CPP
tags: [CPP]
description: 封闭类是在类中包含其他类的成员函数,本文通过一个示例展示封闭类的构造函数、封闭类的对象成员的构造函数、析构函数的调用顺序以及成员复制构造函数的调用等问题。C++11新特性通过default显示定义默认构造函数以及delete来显示禁用默认构造函数和复制构造函数。
---


# 构造函数

构造函数是与类名相同的，无返回值的函数，主要作用是对对象初始化，若没有写构造函数则编译器会自动生成一个默认的无参数的构造函数。构造函数遵守下面的规则：
- 默认构造函数无输入参数，不进行任何操作。
- 如果定义了构造函数，那么编译器便不会再生成无参数的默认构造函数。
- 在对象生成时构造函数自动被调用，对象一旦生成，就不会再执行构造函数。
- 一个类可以生成多个参数个数或者类型不同的构造函数。
- 构造函数最好是public的，private构造函数没有办法直接初始化对象，代码示例：
```c++
#include <iostream>
#include <string>
class MyClass
{
public:
	MyClass(std::string name){}
private:
	MyClass(){}
};
int main(void)
{
	//MyClass my;			//error,private
	MyClass my2("Lucy");
  return 0;
}
```

## 构造类、类数组
在实际的使用中，我们可以声明一个数组，new来申请一个类空间、甚至一个数组空间，下面这个示例展示了数组时类的构造函数是如何调用的。
```c++
#include <iostream>
#include <string>
class Circle
{
public:
	Circle(){
		_r = 0;
		std::cout << "Construct Circle without Parameter" << std::endl;
	}
	Circle(double r){
		_r = r;
		std::cout << "Construct Circle with " << r << std::endl;
	}
	double area(void){
		std::cout << "Circle's area: " << 3.1415926 * _r * _r << std::endl;
		return 3.1415926 * _r * _r;
	}
	~Circle(){
		std::cout << "Deconstruct Circle r = " << _r << std::endl;
	}
private:
	double _r;
};
int main(void)
{
	Circle c1[2];
	std::cout << std::endl << std::endl;

	Circle c2[2] = { 1 };
	std::cout << std::endl << std::endl;

	Circle c3[2] = { 2, 3 };
	std::cout << std::endl << std::endl;

	Circle* a1 = new Circle[2];
	delete[]a1;
	std::cout << std::endl << std::endl;

	Circle a2[3] = { 1, Circle(2) };
	std::cout << std::endl << std::endl;

	Circle* a3[3] = { new Circle(), new Circle(1) }; //a3(2) NULL
	for (int i = 0; i < 2; i++){
		a3[i]->area();
		delete a3[i];
	}
	std::cout << std::endl << std::endl;
	system("pause");
	return 0;
}
```
运行结果
```
Construct Circle without Parameter
Construct Circle without Parameter

Construct Circle with 1
Construct Circle without Parameter

Construct Circle with 2
Construct Circle with 3

Construct Circle without Parameter
Construct Circle without Parameter
Deconstruct Circle r = 0
Deconstruct Circle r = 0

Construct Circle with 1
Construct Circle with 2
Construct Circle without Parameter

Construct Circle without Parameter
Construct Circle with 1
Circle's area: 0
Deconstruct Circle r = 0
Circle's area: 3.14159
Deconstruct Circle r = 1
```

## 类型自动转化构造函数

定义转换构造函数是为了实现类型的自动转换。在需要时，编译系统会自动调用转换构造函数，建立一个临时变量。
```c++
#include <iostream>
#include <string>
class Circle
{
public:
	Circle(double r){
		_r = r;
		std::cout << "Construct Circle with " << r << std::endl;
	}
	Circle(double r, int x, int y){
		std::cout << "Construct Circle with " << r << " ("
			<< x << "," << y << ")" << std::endl;
	}
	~Circle(){
		std::cout << "Deconstruct Circle " << _r << std::endl;
	}
private:
	double _r;
};
int main(void)
{
	Circle c1(10,4.5,6.8);
	Circle c2 = 16.8;        //构造一个临时Circle(10),之后复制给c2
	std::cout << std::endl << std::endl;
	system("pause");
	return 0;
}
```
运行结果
```
Construct Circle with 10(4,6)
Construct Circle with  16.8
```
## 抑制构造函数隐式转化

explicit构造函数会抑制构造函数的隐式转换，只能用于直接初始化。而发生隐式转换的一种情况是，执行拷贝形式的初始化时(直接使用=)。使用explicit需要注意以下注意事项：
  - explicit关键字只能在类内构造函数中使用。
  - explicit关键字只能对一个实参的构造函数使用，多个实参的构造函数无法进行隐形转化，因此无需指定。

下面的代码中Circle c = 10;不能成功的原因在进行赋值之前会先将10强制类型转化为Circle(10),之后再赋值给c，而此种的类型转换是不允许的，故失败。下面的代码中Circle c3 = c1;由于不能将explicit构造函数用于拷贝形式的初始化过程，导致这样的形式是不允许的，在Vs2013中该表达式调用的相关类的复制构造函数，故没有出现报错。

```c++
#include <iostream>
#include <string>
class Circle
{
public:
	explicit Circle(double r){
		_r = r;
		std::cout << "Construct Circle with " << r << std::endl;
	}
	double r(void)			{ return _r; }
private:
	double _r;
};

int main(void)
{
//	Circle c = 10;				//无法从int转换为Circle
	Circle c0 = static_cast<Circle>(10);	// static_cast进行显式的非隐式转化
	Circle c1(10);
	Circle c2(c1);				//直接初始化
	std::cout << "c2.r " << c2.r() << std::endl;
	Circle c3 = c1;				//allowed,call copy construct VS2013
	std::cout << "c3.r " << c3.r() << std::endl;
	system("pause");
	return 0;
}
```
运行结果
```
Construct Circle with 10
Construct Circle with 10
c2.r 10
c3.r 10
```

# 复制(拷贝)构造函数
复制构造函数用于用同种类型的对象去初始化一个对象的过程中要做什么。如果没有定义复制构造函数，那么编译器会自动生成默认的复制构造函数，默认的复制构造函数完成复制功能。<br/>
复制构造函数形如X::X(X&)或X::X(const X&)，二选其一。不允许形如X::X(X)的构造函数<br/>
复制构造函数被调用有如下三种情况：
- 1 用一个对象去初始化同类的另一个对象。
- 2 若函数参数是类X的对象，那么该函数被调用时，类X的复制构造函数将被调用。
- 3 若函数的返回值是类X的对象，那么函数返回时，类X的复制构造函数将被调用。
- 4 用=定义变量
```c++
#include <iostream>
#include <string>
class Circle
{
public:
	Circle(double r){
		_r = r;
		std::cout << "Construct Circle with " << r << std::endl;
	}
	Circle(const Circle& c){
		_r = c._r;
		std::cout << "Copy Construct " << _r << std::endl;
	}
	void copyCircle(const Circle& c){
		std::cout << "The radius of copied circle " << c._r << std::endl;
	}
	Circle& copyCircle2(double r){
		Circle c(r);
		return c;
	}
	~Circle(){
		std::cout << "Deconstruct Circle " << _r << std::endl;
	}
private:
	double _r;
};
int main(void)
{
	Circle c1(1);
	Circle c2(c1);			//condition 1
	std::cout << std::endl << std::endl;

	Circle c3 = c1;			//copy construct’s called,VS2013
	std::cout << std::endl << std::endl;

	Circle c22(6.8);
	c3.copyCircle(c22);		//condition 2
	std::cout << std::endl << std::endl;

	c3.copyCircle2(4.8);	//condition 3
	std::cout << std::endl << std::endl;
	system("pause");
	return 0;
}
```
运行结果
```
Construct Circle with 1
Copy Construct 1


Copy Construct 1


Construct Circle with 6.8
The radius of copied circle 6.8


Construct Circle with 4.8
Deconstruct Circle 4.8
```

# 合成拷贝构造函数
X& operator=(const X& x)进行运算符=的重载来完成实现拷贝初始化，默认如果系统没有定义,复制拷贝运算符重载时，编译器会自动生成。

```c++
#include <iostream>
#include <string>
class Circle
{
public:
	Circle(double r){
		_r = r;
		std::cout << "Construct Circle with " << r << std::endl;
	}
	Circle(const Circle& c){
		_r = c._r;
		std::cout << "Copy Construct " << _r << std::endl;
	}
	Circle& operator=(const Circle& c){
		_r = c._r;
		std::cout << "copy operator = " << _r << std::endl;
		return *this;
	}
	~Circle(){
		std::cout << "Deconstruct Circle " << _r << std::endl;
	}
private:
	double _r;
};
int main(void)
{
	Circle c0(10);
	Circle c1 = c0;
	Circle c2(1);
	c2 = c0;
	system("pause");
	return 0;
}
```
运行结果
```
Construct Circle with 10
Copy Construct 10
Construct Circle with 1
Copy Construct = 10
```

# 析构函数

析构函数在类消亡时自动调用，来进行一些善后工作，比如释放分配的空间。
- 名字与类名相同，在其前面添加~。该函数没有返回值。
- 一个类中最多只有一个析构函数
- 如果没有定义析构函数，则编译器会自动生成缺省的析构函数，缺省的析构函数不做任何事情。若定义了析构函数，则编译器则不生成缺省的析构函数。<br/>

构造函数调用的情景
- 1 类的生命周期结束
- 2 类数组的生命周期结束时，析构函数被调用的次数等于类数组中元素的个数
- 3 delete会导致自购函数的调用，同样地对于类数据，析构函数被调用的次数等于数组成员的个数。
- 4 当类作为函数的返回值，在函数返回时，析构函数被调用。

```c++
#include <iostream>
#include <string>
class Circle
{
public:
	Circle(){
		_r = 0;
		std::cout << "Construct Circle without Parameter " << _r << std::endl;
	}
	Circle(double r){
		_r = r;
		std::cout << "Construct Circle with " << r << std::endl;
	}
	Circle(double r, int x, int y){
		_r = r;
		std::cout << "Construct Circle with " << r << " ("
			<< x << "," << y << ")" << std::endl;
	}
	~Circle(){
		std::cout << "Deconstruct Circle " << _r << std::endl;
	}
	Circle& copyCircle2(double r){
		Circle c(r);
		return c;
	}
private:
	double _r;
};
int main(void)
{
	Circle c1(3.4);
	std::cout << std::endl << std::endl;

	Circle* c2 = new Circle(3.4, 6, 7);
	c2->copyCircle2(6.8);
	delete c2;
	std::cout << std::endl << std::endl;

	Circle a1[2];
	std::cout << std::endl << std::endl;
	Circle *a2 = new Circle[2];
	delete[]a2;

	std::cout << std::endl << std::endl;
	system("pause");
	return 0;
}
```
运行结果
```
Construct Circle with 3.4

Construct Circle with 3.4 (6,7)
Construct Circle with 6.8
Deconstruct Circle 6.8
Deconstruct Circle 3.4

Construct Circle without Parameter 0
Construct Circle without Parameter 0

Construct Circle without Parameter 0
Construct Circle without Parameter 0
Deconstruct Circle 0
Deconstruct Circle 0
```
## 封闭类的构造、析构、复制构造

封闭类中，我们必须让其中的对象成员明确其是如何初始化的，具体实现时，我们需要在封闭类的构造函数中完成参数列表的初始化。

- 1 封闭类对象生成时先执行所有对象成员的构造函数，再执行封闭类的构造函数。
- 2 对象成员的构造次序和成员在类中说明次序一致，与它们在成员初始化列表中的顺序无关。
- 3 封闭类在析构消亡时，先执行封闭类的析构函数，然后执行成员对象的析构函数。析构时的执行次序和构造函数的调用次序相反。
- 4 封闭类用类进行构造时，如果对象成员定义了复制构造函数则调用复制构造函数对其成员对象初始化。

```C++
#pragma once
#include <iostream>
#include <string>
class Engine
{
public:
	Engine(std::string manu);
	~Engine();
	Engine(Engine& engine);
	std::string manu(void) { return _manu; }
private:
	std::string  _manu;
};

class Tyre {
public:
	Tyre(int radius);
	~Tyre();
private:
	int _radius;
};

class Car
{
public:
	Car(int radius, std::string manu);
	~Car();
private:
	Engine engine;
	Tyre tyre;
};
```
```C++
#include "Car.h"
Engine::Engine(std::string manu):
	_manu(manu)
{
	std::cout << "Construct Engine with manufacture " << _manu << std::endl;
}

Engine::~Engine()
{
	std::cout << "Deconstruct Engine whose manufacture is " << _manu << std::endl;
}

Engine::Engine(Engine & engine)
{
	_manu = engine.manu();
	std::cout << "Engine's copy constructor " << _manu << std::endl;
}

Tyre::Tyre(int radius):
	_radius(radius)
{
	std::cout << "Construct Tyre with radius " << _radius << std::endl;
}
Tyre::~Tyre()
{
	std::cout << "Deconstruct Tyre whose radius is " << _radius << std::endl;
}

Car::Car(int radius, std::string manu):
	tyre(radius),
	engine(manu)
{
	std::cout << "Construct Car with tyre's radius " << radius 
		<< "and engine's manufacture " << manu << std::endl;
}

Car::~Car()
{
	std::cout << "Deconstruct Car " << std::endl;
}
```
```C++ 
#include "Car.h"
int main(int argc, char* argv[])
{
	Car car(20, std::string("Jili"));
	Car car2(car);
	system("pause");
	return 0;
}
```
运行结果：
```
Construct Engine with manufacture Jili
Construct Tyre with radius 20
Construct Car with tyre's radius 20and engine's manufacture
Engine's copy constructor Jili
Deconstruct Car 
Deconstruct Tyre whose radius is 20
Deconstruct Engine whose manufacture is Jili
```



## C++11新特性之default和 delete

### default特性

​	C++函数会类生成默认的构造函数、析构函数、复制构造函数，而当我们手动定义了构造函数之后，编译器便不再自动生成构造函数，此时需要手动定义，从而形成如下问题：

- 增大了程序员的工作量
- 无法获得编译器自动生成的默认成员函数的高性能。

C++11引入了default函数，允许编译器显式声明自动定义的函数体。

```C++
class A{
public:
	int num;
	A(int n){ num = n; }
};
```

此时

```C++
A tmp;					//error由于没有A()构造函数
```

为解决上述问题，可以添加```A(){};```而这样代码的执行效率是比较低的，我们可以利用default实现显示调用编译器自定义的函数体。

```C++
A() = default;
```



### delete特性

​	在C++中，如果没有手动添加构造函数、拷贝构造函数和复制构造函数，那么编译器会自动隐士生成一个默认的成员函数，有时我们希望禁用这样的功能，C++11引入了delete函数，允许显式禁用这些功能。

```C++
class A{
public:
	int num;
	A(const A & a) = delete;
	A(A && a){}
	A(int n){ num = n; }
};
```

那么，下面的代码会出现问题

```C++
	A tmp1(tmp);			//error，不允许复制
	A tmp0(A(8));			//允许移动
```