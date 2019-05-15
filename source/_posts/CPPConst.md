---
layout: post
title: const修饰符
date: 2017-3-16 18:04
category: CPP
tags: [CPP]
description: 对于const变量，C++编译器在编译期间会将用到该变量的地方替换为对应的数值。
	1、运行时，引入常量折叠的概念。
	2、常量成员函数，在参数列表后追加const。
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



#### 常量成员函数

​	在参数列表后面紧跟const关键字，此处的**const关键字隐式修改this指针的类型为常量指针。**常量成员函数无法修改它调用对象的内容，在使用中有如下注意事项：

- **常量对象、常量对象引用或指针都只能调用常量成员函数。**示例如下，其中[BulkQuote定义](https://github.com/DepInjoy/BaseHouse/blob/master/CPP/%E7%BB%A7%E6%89%BF/%E7%BB%A7%E6%89%BF%E7%AE%80%E5%8D%95%E4%BD%BF%E7%94%A8%E6%B5%8B%E8%AF%95%E7%A4%BA%E4%BE%8B.cpp)

```C++
	const BulkQuote constBulkQuote;
	std::string name3 = constBulkQuote.name3();
	std::cout << name3 << std::endl;				//BulkQuote - Const function
	//下面的操作失败，这是由于常量对象无法访问非常量成员函数
	// name3 = constBulkQuote.name1();
	std::cout << std::endl;

	BulkQuote bulkQuote0;
	std::cout << bulkQuote0.name3() << std::endl;	//BulkQuote - Const function
```

- **常量成员函数不可修改其对象中的内容。**

```C++
std::string name3() const{
    //_protectedValue为该类中的一个protected变量，常量成员函数无法修改对象内容
	//_protectedValue = 444;
	return "BulkQuote - Const function";
}
```

