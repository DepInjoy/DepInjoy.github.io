---
layout: post
title: 设计模式：工厂模式
date: 2019-2-11 21：06
category:设计模式
tags: [设计模式]
description: 本文以计算器为例，首先以简单的switch进行实现，分析其耦合度高，不便于复用和维护，并通过面向对象编程实现Operation父类，再生成OperationAdd、OperationSub、OperationMulti、OperationDiv分别对应于加法、减法、乘法和除法的处理，并通过简单工厂进行测试验证。
---

​	本文以《大话设计模式》为主轴，开始设计模式的学习，并以C++进行实现。

### 简单工厂模式

​	简单工程模式以实现计算器为例，首先，看下面的实现代码：

```C++
	float a, b, res;
	char op;
	std::cout << "Please input first number: " << std::endl;
	std::cin >> a;
	std::cout << "Please select operation(+, -, *, /)" << std::endl;
	std::cin >> op;
	std::cout << "Please input second number: " << std::endl;
	std::cin >> b;
	switch (op)
	{
	case '+':
		res = a + b;
		break;
	case '-':
		res = a - b;
		break;
	case '*':
		res = a * b;
		break;
	case '/':
		if (b == 0) res = FLT_MAX;
		else res = a / b;
		break;
	default:
		break;
	}
	std::cout << a << " " << op << " " <<  b << " = " << res << std::endl;
```

上述代码，完成了基础的功能，却存不易维护，不易扩展和不易复用的问题。我们可以利用面向对象编程通过封装、继承和多态将程序的耦合度降低，再利用设计模式将程序设计得更加灵活、易修改、易复用。



#### 面向对象编程

​	上述实现，完成了基础的功能，而代码的耦合度高，比如如果需要新增平方和立方运算，或者时多种运算结合时，代码不易维护，而面向对象编程的继承可以较好地解决这个问题。

```C++
#include <iostream>
//运算操作，父类
class Operation
{
public:
    //设置第一运算数
	void setNumA(float a) { numA = a; }	
	//设置第二运算数
    void setNumB(float b) { numB = b; }	
    //返回运算结果
	virtual float getResult(void) { return 0; }
protected:
	float numA;						// 第一运算数
	float numB;						// 第二运算数
};
//加法类
class OperationAdd : public Operation
{
public:
	float getResult(void) { return numA + numB; }
};
//减法类
class OperationSub : public Operation
{
public:
	float getResult(void) { return numA - numB; }
};
//乘法类
class OperationMulti : public Operation
{
public:
	float getResult(void) { return numA * numB; }
};
//除法类
class OperationDiv : public Operation
{
public:
	float getResult(void) {
		if (numB == 0)	return FLT_MAX;
		else return numA / numB;
	}

};
```



#### 简单工厂模式

```C++
class SimpleFactoryDemo
{
public:
	static Operation* createFactory(char op);
};
```

```C++
Operation* SimpleFactoryDemo::createFactory(char op)
{
	Operation* operation = NULL;
	switch (op)
	{
	case '+':
		operation = new OperationAdd();
		break;
	case '-':
		operation = new OperationSub();
		break;
	case '*':
		operation = new OperationMulti();
		break;
	case '/':
		operation = new OperationDiv();
		break;
	default:
		break;
	}
	return operation;
}

```

测试代码：

```C++
	float a, b;
	std::cout << "Please input your first number : " << std::endl;
	std::cin >> a ;
	std::cout << "Please input your second number : " << std::endl;
	std::cin >> b;
	//创建一个加法运算符
	Operation* opAdd =  SimpleFactoryDemo::createFactory('+');
	opAdd->setNumA(a);											//2
	opAdd->setNumB(b);											//8
	std::cout << opAdd->getResult() << std::endl;				//10
```

在此基础上，如果我们希望添加平方和立方，只需要在此重写一个继承Operation的类，并实现getResult接，在createFactory类中新增一个switch即可。