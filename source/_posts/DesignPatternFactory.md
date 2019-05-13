---
layout: post
title: 设计模式：工厂模式
date: 2019-2-11 21：06
category:设计模式
tags: [设计模式]
description: 本文以计算器为例，首先以简单的switch进行实现，分析其耦合度高，不便于复用和维护，并通过面向对象编程实现Operation父类，再生成OperationAdd、OperationSub、OperationMulti、OperationDiv分别对应于加法、减法、乘法和除法的处理，并通过简单工厂进行测试验证。
---

本文以《大话设计模式》为主轴，开始设计模式的学习，并以C++进行实现。

### [简单工厂模式(Simple Factory Pattern)](https://design-patterns.readthedocs.io/zh_CN/latest/creational_patterns/simple_factory.html)

​	又称静态工厂方法(Static Factory Method)模式，属于类创建型模式。该模式中，可以根据参数不同返回不同类的实例。简单工厂模式专门定义一个类来负责创建其他类的实例，被创建的实例通常都具有共同的父类。

#### 应用示例：计算器	

​	下面以实现计算器为例来展示简单工程模式，首先，看下面的实现代码：

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

​	上述代码，完成了基础的功能，却存不易维护，不易扩展和不易复用的问题。我们可以利用面向对象编程通过封装、继承和多态将程序的耦合度降低，再利用设计模式将程序设计得更加灵活、易修改、易复用。

##### 面向对象+简单工厂

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

```C++
class SimpleFactoryDemo
{
public:
	static Operation* createFactory(char op);
};
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

​	在此基础上，如果我们希望添加平方和立方，只需要在此重写一个继承Operation的类，并实现getResult接，在createFactory类中新增一个switch即可。



#### 优缺点

##### 优点

- 将对象的创建和业务逻辑分离降低系统的耦合度。
- 当你需要什么，只需要传入一个正确的参数，就可以获取你所需要的对象，而无须知道其创建细节。
- 由于工厂方法是静态方法，使用起来很方便，可通过类名直接调用，而且只需要传入一个简单的参数即可，在实际开发中，还可以在调用时将所传入的参数保存在XML等配置文件，修改参数时无须修改任何源代码，这在一定程度上提高了系统的灵活性。
- 工厂类含有必要的判断逻辑，可以决定在什么时候创建哪一个产品类的实例，客户端可以免除直接创建产品对象的责任，而仅仅“消费”产品；简单工厂模式通过这种做法实现了责任分割，它提供了专门的工厂类用于创建对象。



##### 缺点

- 系统扩展困难，一旦添加新产品就不得不修改工厂逻辑，在产品类型较多时，有可能造成工厂逻辑过于复杂，不利于系统的扩展和维护。
- 工厂类的职责相对过重，增加新的产品需要修改工厂类的判断逻辑，这一点与开闭原则是相违背的。
- 由于使用了静态工厂方法，造成工厂角色无法形成基于继承的等级结构。
- 由于工厂类集中了所有产品创建逻辑，一旦不能正常工作，整个系统都要受到影响。



##### 适用环境

- **工厂类负责创建的对象比较少。**由于创建的对象较少，不会造成工厂方法中的业务逻辑太过复杂。
- **客户端只知道传入工厂类的参数，对于如何创建对象不关心。**客户端既不需要关心创建细节，甚至连类名都不需要记住，只需要知道类型所对应的参数。

### 抽象工厂模式

P140