---
layout: post
title: 构造、析构和复制构造函数
date: 2018-03-23 17:24
category: CPP
tags: [CPP]
description: 封闭类是在类中包含其他类的成员函数,本文通过一个示例展示封闭类的构造函数、封闭类的对象成员的构造函数、析构函数的调用顺序以及成员复制构造函数的调用等问题。
	1、构造函数使用和注意事项	
	2、C++11新特性通过default显示要求生成合成函数以及delete来删除默认构造、复制构造函数和拷贝构造函数。
---



### 构造函数

​        构造函数是与类名相同的，无返回值的函数，主要作用是对对象初始化，若没有写构造函数则编译器会自动生成一个默认的无参数的构造函数。构造函数遵守下面的规则：
- 默认构造函数无输入参数，不进行任何操作。**当对象被默认初始化或者之初始化时自动执行默认构造函数**。

  下面这些情况发生值初始化：

  - **数组初始化过程中，如果初始值数量少于数组的大小时，自动执行值初始化(此时会自动执行默认构造函数)。**[示例类的定义](https://github.com/DepInjoy/BaseHouse/blob/master/CPP/%E6%9E%84%E9%80%A0%E5%92%8C%E6%9E%90%E6%9E%84/%E6%9E%84%E9%80%A0%E7%B1%BB%E6%95%B0%E7%BB%84%E7%A4%BA%E4%BE%8B.cpp) 

  ```C++
  /*
  运行结果：
  	Construct Circle with 1
  	Construct Circle without Parameter
  */
  Circle c2[2] = { 1 };
  delete[]c2;
  ```

  - 不使用初始值定义一个局部静态变量。

  ```C++
  static Circle c;
  ```

- 如果定义了构造函数，那么编译器便不会再生成无参数的默认构造函数（也称合成默认构造函数）。

- 在对象生成时构造函数自动被调用，对象一旦生成，就不会再执行构造函数。

- 一个类可以生成多个参数个数或者类型不同的构造函数，类似于函数重载。

- 构造函数最好是public的，private构造函数没有办法直接初始化对象（单例利用该特性避免多次构造），代码示例：
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
#### 成员初始化顺序

- **构造函数初始值列表，成员的初始化顺序和他们在类中的定义顺序保持一致。**构造函数初始化列表中初始化之的前后位置关系不会影响实际的初始化顺序。



#### 委托构造函数

​	C++11新标准，允许定义委托构造函数将其所属类的其他构造函数执行它的初始化过程。

```C++
lass Circle
{
public:
	//非委托构造函数
	Circle(double r){
		std::cout << "Construct Circle with " << r << std::endl;
	}
	//委托构造函数
	Circle(): Circle(0){
		std::cout << "Construct Circle without Parameter" << std::endl;
	}
};
```



#### 转换构造函数

​	**构造函数只接受一个实参，实际上就为它建立了此种类型的隐式转换机制，将这种构造函数称作转换构造函数。**需要注意的是，编译器只会执行一次类型转换。定义转换构造函数是为了实现类型的自动转换。在需要时，编译系统会自动调用转换构造函数，建立一个临时变量。
```c++
//Construct Circle with  16.8
Circle c2 = 16.8;        //构造一个临时Circle(10),之后复制给c2
```

#### 抑制构造函数隐式转化

​        explicit构造函数会抑制构造函数的隐式转换，explicit只能用于直接初始化。而发生隐式转换的一种情况是，执行拷贝形式的初始化时(直接使用=)。使用explicit需要注意以下注意事项：
  - explicit关键字只能在类内构造函数中使用。
  - explicit关键字只能对一个实参的构造函数使用，多个实参的构造函数无法进行隐形转化，因此无需指定。

下面的代码中Circle c = 10;不能成功的原因在进行赋值之前会先将10强制类型转化为Circle(10),之后再赋值给c，而此种的类型转换是不允许的，故失败。下面的代码中Circle c3 = c1;由于不能将explicit构造函数用于拷贝形式的初始化过程，导致这样的形式是不允许的，在Vs2013中该表达式调用的相关类的复制构造函数，故没有出现报错。

```c++
	//	Circle c = 10;				//无法从int转换为Circle
	//Construct Circle with 10
	Circle c0 = static_cast<Circle>(10);	// static_cast进行显式的非隐式转化
	//Construct Circle with 10
	Circle c1(10);
	Circle c2(c1);				//直接初始化
	std::cout << "c2.r " << c2.r() << std::endl;			//10
	Circle c3 = c1;				//10,拷贝初始化
```


**构造函数初始值必不可少的情况**:

- **成员是const或者引用的话，必须进行初始化。**显式为const、引用或者某种未听默认构造函数的类类型，必须通过构造函数初始化列表为这些成员初始化。

  ```C++
  class A{
      A(int ii):
  	i(ii),ci(ii),ri(ii){}
  private:
  	int i;
  	const int ci;
  	int& ri;
  };
  ```



### 复制(拷贝)构造函数

复制构造函数用于用同种类型的对象去初始化一个对象的过程中要做什么。如果没有定义复制构造函数，那么编译器会自动生成默认的复制构造函数，默认的复制构造函数完成复制功能。<br/>
复制构造函数形如X::X(X&)或X::X(const X&)，二选其一。不允许形如X::X(X)的构造函数<br/>
复制构造函数被调用有如下三种情况：
- 1 用一个对象去初始化同类的另一个对象。

  ```C++
  /*
  	Construct Circle with 1
  	Copy Construct 1	
  */	
  Circle c1(1);
  Circle c2(c1);
  ```

- 2 若函数参数是类X的对象，那么该函数被调用时，类X的复制构造函数将被调用。

  ```C++
  /*
  	Construct Circle with 6.8
  	The radius of copied circle 6.8
  */	
  Circle c22(6.8);
  c3.copyCircle(c22);
  ```

- 3 若函数的返回值是类X的对象，那么函数返回时，类X的复制构造函数将被调用。

  ```C++
  /*
  	Construct Circle with 4.8
  	Deconstruct Circle 4.8
  */
  c3.copyCircle2(4.8);
  ```

- 4 用=定义变量

  ```C++
  //Copy Construct 1
  Circle c3 = c1;
  ```
#### 合成拷贝构造函数
X& operator=(const X& x)进行运算符=的重载来完成实现拷贝初始化，默认如果系统没有定义,复制拷贝运算符重载时，编译器会自动生成。

```c++
	/*
		Construct Circle with 10
		Copy Construct 10
		Construct Circle with 1
		Copy Construct = 10
	*/
	Circle c0(10);
	Circle c1 = c0;
	Circle c2(1);
	c2 = c0;
```



### 析构函数

析构函数在类消亡时自动调用，来进行一些善后工作，比如释放分配的空间。
- 名字与类名相同，在其前面添加~。该函数没有返回值。
- 一个类中最多只有一个析构函数
- 如果没有定义析构函数，则编译器会自动生成缺省的析构函数，缺省的析构函数不做任何事情。若定义了析构函数，则编译器则不生成缺省的析构函数。<br/>

**构造函数调用的情景:**

- 1 类的生命周期结束

  ```C++
  	/*
  		Construct Circle with 3.4 (6,7)
  		Construct Circle with 6.8
  		Deconstruct Circle 6.8
  		Deconstruct Circle 3.4
  	*/
  	Circle* c2 = new Circle(3.4, 6, 7);
  	c2->copyCircle2(6.8);
  	delete c2;
  	std::cout << std::endl << std::endl;
  ```

- 2 类数组的生命周期结束时，析构函数被调用的次数等于类数组中元素的个数

  ```C++
  	/*
  		Construct Circle without Parameter 0
  		Construct Circle without Parameter 0
  		Deconstruct Circle 0
  		Deconstruct Circle 0
  	*/
  	Circle a1[2];
  	std::cout << std::endl << std::endl;
  	Circle *a2 = new Circle[2];
  	delete[]a2;
  ```

- 3 delete会导致析构函数的调用，同样地对于类数组，析构函数被调用的次数等于数组成员的个数。

- 4 当类作为函数的返回值，在函数返回时，析构函数被调用。

### 封闭类的构造、析构、复制构造

封闭类中，我们必须让其中的对象成员明确其是如何初始化的，具体实现时，我们需要在封闭类的构造函数中完成参数列表的初始化。

- 1 封闭类对象生成时先执行所有对象成员的构造函数，再执行封闭类的构造函数。
- 2  **对象成员的构造次序和成员在类中说明次序一致，与它们在成员初始化列表中的顺序无关。**
- 3 封闭类在析构消亡时，先执行封闭类的析构函数，然后执行成员对象的析构函数。析构时的执行次序和构造函数的调用次序相反。
- 4 封闭类用类进行构造时，如果对象成员定义了复制构造函数则调用复制构造函数对其成员对象初始化。

### C++11新特性之default和 delete

#### default特性

​	C++函数会类生成默认的构造函数、析构函数、复制构造函数，而当我们手动定义了构造函数之后，编译器便不再自动生成构造函数，此时需要手动定义，从而形成如下问题：增大了程序员的工作量；无法获得编译器自动生成的默认成员函数的高性能。

​	**C++11新标准引入了default函数，允许显式要求编译器生成合成函数。**=default可以出现在类内部和类外部。

- 如果=default出现在类内部，则默认是内联的

  ```C++
  class A{
  public:
  	int num;
      //如果没有此行，A tmp会造成由于没有A()构造函数的错误
      A() = default;				//使用合成构造函数
  	A(int n){ num = n; }
      ~A() = default;				//使用合成析构函数
  };
  ```

- 如果出现在类外部，默认情况下不是内联的。

  ```C++
  class A{
  public:
  	int num;
  	A();
  	A(int n){ num = n; }
  };
  A::A() = default;
  ```



#### delete特性

​	在C++中，如果没有手动添加构造函数、拷贝构造函数和复制构造函数，那么编译器会自动隐士生成一个默认的成员函数，有时我们希望禁用这样的功能，**C++11新标准引入了delete函数，允许显式禁用这些功能。**

```C++
class A{
public:
	int num;
	A(const A & a) = delete;				//阻止拷贝
    A& operator=(const A&) = delete;		//阻止赋值
	A(A && a){}
	A(int n){ num = n; }
};
```

注意，下面这些函数不能是禁止成员：

- **析构函数不能是删除的成员。**如果删除了析构函数，那么编译器将不允许创建该对象或者该对象的临时对象或者释放指向该对象的动态分配对象的指针。
