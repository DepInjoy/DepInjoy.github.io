---
layout: post
title: const修饰符
date: 2017-3-16 18:04
category: CPP
tags: [CPP，C]
description: 对于const变量，C++编译器在编译期间会将用到该变量的地方替换为对应的数值，并引入常量折叠的概念。
---



#### const常量

```C++
#include <iostream>
int main(int argc, char *argv[])
{
	const int a = 10;
	std::cout << "Before change:\t value " << a << " address " << &a << std::endl << std::endl;
	int *p = (int *)&a;			//const显示转换为非const
	*p = 11;
	std::cout << "After change:\t value " << a << " address " << &a << std::endl;
	std::cout << "After change:\t value " << *p << " address " << p << std::endl;
	system("pause");
	return 0;
}
```

运行结果：

```
Before change:  value 10 address 0018F8A8

After change:   value 10 address 0018F8A8
After change:   value 11 address 0018F8A8
```

​	一个地址空间竟然存储了两个值，这是由于**常量折叠**。所谓的常量折叠是指，编译器在语法分析时，对常量表达式计算求值，并用求得的值替换该表达式，并放入常量符号表，进行编译优化。编译器通常不为常量分配内存，但是在编译过程中如果出现对常变量使用extern和&操作，还是会对常量分配空间。但是**对于const变量，C++编译器在编译期间会将用到该变量的地方替换为对应的数值**，而不会使用上述分配的存储空间中的值。



