---
layout: post
title: 面向过程编程和面向对象编程的区别
date: 2018-03-20 20:00
category: C++
tags: [C++]
description: 本文通过一个代码示例展示了面向对象编程思维和处理模式的区别。
---

### 面向对象编程和面向过程编程的区别

```c++
#include <iostream>
class Circle
{
public:
	double r;
	double pi = 3.1415926;
	double s = pi * r * r;
	double getArea(void){ return pi * r * r; }

};

int main(int argc, char *argv[])
{
	Circle circle;
	double tmp = circle.r;
	std::cout << "Before change " << std::endl;
	std::cout << "Area is " << circle.pi * tmp * tmp << std::endl << std::endl;
	circle.r = 10;
	std::cout << "After Change " << std::endl;
	std::cout << "Area is " << circle.s << std::endl;                           //random value
	std::cout << "Area is " << circle.getArea() << std::endl;                   //right value
	system("pause");
	return 0;
}
```
运行结果：
```
Before change
Area is 2.69149e+124

After Change
Area is 2.69149e+124
Area is 314.159
```
