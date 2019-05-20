---
layout: post
title: 继承和动态绑定
date: 2018-3-22 18:04
category: CPP
tags: [CPP]
description: 本文主要描述了C++中派生类和基类的访问权限、虚函数、纯虚函数以动态绑定的概念，并通过实例来展示对该部分的理解。
---

[C/C++杂记：运行时类型识别（RTTI）与动态类型转换原理](https://www.cnblogs.com/malecrab/p/5574070.html)

面对对象编程的核心思想：

- 数据抽象：实现类接口和实现分离。
- 继承：满足定义类似的的类型对其相似性进行建模。
- 动态绑定：我们可以定义类似但不完全相同的类，却可以在一定程度上忽略相似类型的区别，而用统一的方式使用对象。

### 继承和动态绑定

​	派生类对象的体积等于基类对象的体积加上派生类对象自己成员变量的体积。在派生类对象中，包含基类对象，而且基类对象的存储位置位于派生类新增成员变量之前。



#### 派生类的构造和析构函数

​	**创建派生类对象时，先调用基类的构造函数来初始化派生类从基类继承的成员。同样派生类的析构函数执行时，先执行派生类的析构函数再执行基类的析构函数。**

派生类调用基类构造函数的方法有两种：

- 显式调用，在派生类的构造函数中，为基类构造函数提供参数.

  派生类::类名(参数列表):基类(参数列表)

- 隐式调用，派生类的构造函数中，省略基类构造函数时，派生类构造函数自动调用基类的默认构造函数。

#### 访问权限
派生类具有基类全部的成员函数和成员变量，包含private、protected、public。那么基类是如何控制其派生类的访问权限的呢？在实际的开发中我们可以在基类中定义一些protected属性的变量，在基类中进行共享。

|属性|派生类|基类|其他|
|:---:|:---:|:---:|-----|
|public|√|√|√|
|protected|√(当前类)|√|×|
|private|×|√|×|

#### public继承的赋值兼容规则

```
class base{};
class derived:public base{};
base b;
derived d;
```

- 派生类对象可以赋值给基类的对象。

  b = d;

- 派生类对象可以初始化基类引用

  base &b1 = d;

- 派生类对象的地址可以赋值给基类的指针

  base * b2 = &d

#### 多态

​	多态实现的关键是虚函数表，在每一个包含虚函数的类中都存在一个虚函数表，该类的任何对象中都放着虚函数的指针(4个字节)，多态增加了函数调用的空间和时间的消耗，却可以增强代码的可扩充性。

​	多态函数的调用语句被编译成一系列根据基类指针所指向的（或基类引用所引用的）对象中存放的虚函数表的地址，在虚函数表中查找虚函数地址并调用虚函数指令。下面的这个示例可以用很好的展示对这部分的理解：

```C++
#include <iostream>
class Base
{
public:
	Base(){};
	~Base(){ std::cout << "Destruct Base" << std::endl; }
	virtual void func(){ std::cout << "Base::func" << std::endl; }
private:
	int i;							//sizeof(int) = 4
};

 class Derived : public Base
 {
 public:
	 Derived(){};
	 ~Derived(){ std::cout << "Destruct Derived" << std::endl; }
	 virtual void func(){ std::cout << "Derived::func" << std::endl; }
 };

 int main(int argc, char*argv[])
 {
	 std::cout << sizeof(Base) << " " << sizeof(Derived) << std::endl;	//8 8
	 Base b;
	 Base *d = new Derived();			//Derived::func
	 d->func();
	 long long *p1 = (long long*)&b;
	 long long *p2 = (long long*)d;
	 *p2 = *p1;
	 d->func();							//Base::func
	 delete d;
     return 0;
 }
```

#### 虚析构函数

​	我们删除基类指针释放对象时，调用的是基类的析构函数，同样地默认的定义的派生类的析构函数，当我们删除派生类时，调用的是派生类的析构函数，在实际的使用中，很多时候析构派生类时，需要调用基类的析构函数，此时我们需要在基类析构函数前添加virtual将其声明虚析构函数。派生类的虚析构函数定义可以省略virtual关键字。虚析构函数调用顺序，首先调用派生类的析构函数，然后调用基类的虚构函数。

```C++
#include <iostream>
class Base
{
public:
    /*
    	此处如果不添加virtual关键字，则派生类析构不会调用基类的析构函数。
    	如果希望析构派生类时，希望调用基类的析构，则需要添加virtual关键字
    */
	virtual ~Base(){ std::cout << "Destruct Base" << std::endl; }
};

class Derived : public Base
{
public:
	~Derived(){ std::cout << "Destruct Derived" << std::endl; }
private:
};
int main(int argc, char*argv[])
{
	/*
	Destruct Derived
	Destruct Base
	*/
	Base *d = new Derived();
	delete d;
	system("pause");
	return 0;
}
```

基类中存在两种函数：

- 一类是基类希望派生类进行覆盖的函数，我们需要将其定义为虚函数。虚函数是唯一可以进行覆盖的，虚函数的定义是在函数声明前添加**关键词virtual**。C++11的新属性中，我们可以添加**override关键词**来表明派生类中的虚函数，同时显式地注明使用哪个成员函数来重写基类的虚函数，如果派生类没有进行覆盖操作，那么编译器将会报错，相反地我们也可以将一个函数添加**final关键词**，之后如果存在尝试性的覆盖，将会引发错误。我们也可以在虚函数的尾部追加=0来**声明纯虚函数来提醒开发人员在派生类中进行相关的实现**，纯虚函数在基类中没有实现，在派生类中必须进行相关的实现，否则，编译器会报错。
- 一类是基类希望派生类直接继承不要进行覆盖。我们将其声明为普通的函数，需要注意的是该函数需要时public或protected属性，派生类才可以正常的访问。

```C++
#include <iostream>
#include <string>
class Base
{
public:
	Base();
	Base(std::string name);
	virtual void printInfo(void);
private:
};

Base::Base()
{
	std::cout << "contruct Base ! " << std::endl;
}
Base::Base(std::string name)
{
	std::cout << "Construct Base with" << name << std::endl;
}
void Base::printInfo(void)
{
	std::cout << "Base::printInfo !" << std::endl;
}

class Derived: public Base
{
public:
	Derived();
	virtual void printInfo(void);
private:
};

Derived::Derived()
{
	std::cout << "Construct Derived !" << std::endl;
}
void Derived::printInfo(void)
{
	std::cout << "Derived::printInfo !" << std::endl;
}
int main(int argc, char* argv[])
{
	Base b;
	Derived d;
	Base& b1 = d;
	b1.printInfo();				//Derived::printInfo
	b1 = b;
	b1.printInfo();				//Derived::printInfo        ???

	Base& b11 = b;
	b11.printInfo();			//Base::printInfo !
	b11 = d;
	b11.printInfo();			//Base::printInfo !			???

	Base* b2 = &d;				//Derived::printInfo
	b2->printInfo();
	b2 = &b;
	b2->printInfo();			//Base::printInfo !

	Base* b21 = &b;
	b21->printInfo();			//Base::printInfo !
	b21 = &d;
	b21->printInfo();			//Derived::printInfo
	system("pause");
	return 0;
}
```



#### 动态绑定

- 运行时绑定：在函数运行时，由传入的实参来选择函数。当我们使用基类的引用（或指针）调用一个虚函数时会发生动态绑定。[演示示例](https://github.com/DepInjoy/BaseHouse/blob/master/CPP/%E7%BB%A7%E6%89%BF/%E7%BB%A7%E6%89%BF%E7%AE%80%E5%8D%95%E4%BD%BF%E7%94%A8%E6%B5%8B%E8%AF%95%E7%A4%BA%E4%BE%8B.cpp)
- [C/C++杂记：运行时类型识别（RTTI）与动态类型转换原理](https://www.cnblogs.com/malecrab/p/5574070.html)[C/C++杂记：运行时类型识别（RTTI）与动态类型转换原理](https://www.cnblogs.com/malecrab/p/5574070.html)
- 

