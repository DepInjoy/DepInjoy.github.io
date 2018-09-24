---
layout: post
title: 友元
date: 2018-03-25 20:00
category: CPP
tags: [CPP]
description: 友元提供了一种其他的类可以访问该类中的private成员的方式，存在友元函数和友元类两种，本文通过一个示例展示了使用方法。
---

C++通过友元函数可以访问类的private成员，通过 
```
friend 返回值类型 类名::函数名称(形参列表...);
```

声明。若类A作为类B的友元时，类A称为友元类。A所有成员函数可以访问B所有成员。

**需要注意的是**：
- 友元关系不可以被传递、继承。若类B是类A的友元，类C是类B的友元，类C不一定是类A的友元，取决于是否有相应的声明。
- 只有友元函数可以访问类的private成员，而友元类中的所有的函数都是友元函数，可以访问类中的所有的成员。

```C++
#pragma once
#include <iostream>
#include <string>
#include <list>
#include <cfloat>
class Wheel;
class Mcar;
class Driver;
class MCar
{
public:
	MCar(float price = 10000);
	~MCar();
	void run(Wheel& wheel);
	friend class Driver;
private:
	float price;
};

class Wheel
{
public:
	Wheel(int r, std::string name);
	~Wheel();
	void setSpeed(float speed);
	friend void MCar::run(Wheel& wheel);
private:
	void display(void);
	int _radius;
	std::string _name;
	float _speed;
};

class Driver {
public:
	void myInfo();
private:
	MCar myCar;
};
```
```C++
#include "MCar.h"
Wheel::Wheel(int r, std::string name):
	_speed(0.0),
	_radius(r),
	_name(name)
{}
Wheel::~Wheel()
{}

void Wheel::setSpeed(float speed)
{
	if (_speed != speed)
		_speed = speed;
}

void Wheel::display(void)
{
	std::cout << "Name: " << _name <<" Speed: " 
		<< _speed << " Radius: " << _radius << std::endl;
}

MCar::MCar(float price):
	price(price)
{
	//not friend, private is not allowed to visit
	//Wheel w;
	//w.display();
}
MCar::~MCar()
{}

void MCar::run(Wheel& wheel)
{
	std::cout << "Car is running with speed " << wheel._speed 
		<< " , name " << wheel._name << " and radius " << wheel._radius << std::endl;
	wheel.display();		//friend function, private is allowed to visit
}

void Driver::myInfo(void)
{
	std::cout << "This my infomation of my cars " << myCar.price << std::endl;
	myCar.price -= 100;
	std::cout << "My cars's price is down to " << myCar.price << std::endl;
}
```
```C++
#include "MCar.h"
int main(int argc, char* argv[])
{
	Wheel wheel(18, std::string("Better"));
	MCar car;
	car.run(wheel);
	Driver driver;
	driver.myInfo();
	system("pause");
	return 0;
}
```
运行结果：
```
Car is running with speed 0 , name Better and radius 18
Name: Better  Speed: 0 Radius: 18
This my infomation of my cars 10000 
My cars's price is down to 9900
```