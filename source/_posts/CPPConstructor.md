---
layout: post
title: 构造函数
date: 2018-03-23 17:24
category: C++
tags: [C++]
description: 封闭类是在类中包含其他类的成员函数,本文通过一个示例展示封闭类的构造函数、封闭类的对象成员的构造函数、析构函数的调用顺序以及成员复制构造函数的调用等问题。
---

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