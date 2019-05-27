---
layout: post
title: 设计模式-工厂模式
date: 2019-2-11 21：06
category: 设计模式
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

[简单工程模式实现计算器](https://github.com/DepInjoy/BaseHouse/blob/master/DesignPattern/%E5%B7%A5%E5%8E%82%E6%A8%A1%E5%BC%8F/%E7%AE%80%E5%8D%95%E5%B7%A5%E5%8E%82%E6%A8%A1%E5%BC%8F-%E8%AE%A1%E7%AE%97%E5%99%A8.cpp)

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



### [工厂方法模式](https://design-patterns.readthedocs.io/zh_CN/latest/creational_patterns/factory_method.html)



​	**工厂方法模式定义了一个用于创建对象的接口，让子类决定实例化哪一个类，工厂方法是一个类的实例化延迟到它的子类，属于类创建型模式**工厂方法把简单工厂的内部逻辑判断移到了客户端代码进行，如果你想要添加功能，在简单工厂模式中需要修改工厂类，而限制只需要修改客户端。

[工厂方法模式实现计算器的示例](https://github.com/DepInjoy/BaseHouse/blob/master/DesignPattern/%E5%B7%A5%E5%8E%82%E6%A8%A1%E5%BC%8F/%E5%B7%A5%E5%8E%82%E6%96%B9%E6%B3%95%E6%A8%A1%E5%BC%8F-%E8%AE%A1%E7%AE%97%E5%99%A8.cpp)



#### 优点(why)

- **工厂方法模式克服了简单工厂模式违背了开放-封闭原则的缺点。**在系统中加入新产品时，无须修改抽象工厂和抽象产品提供的接口，无须修改客户端，也无须修改其他的具体工厂和具体产品，而只要添加一个具体工厂和具体产品就可以了。这样，系统的可扩展性也就变得非常好，完全符合开放-封闭原则

- **基于工厂角色和产品角色的多态性设计是工厂方法模式的关键。**它能够使工厂可以自主确定创建何种产品对象，而如何创建这个对象的细节则完全封装在具体工厂内部。工厂方法模式之所以又被称为多态工厂模式，是因为所有的具体工厂类都具有同一抽象父类。



#### 适用场景

- 一个类不知道它所需要的对象的类
- 一个类通过其子类来指定创建哪个对象
- 将创建对象的任务委托给多个工厂子类中的某一个，客户端在使用时可以无须关心是哪一个工厂子类创建产品子类，需要时再动态指定。



### 抽象工厂模式

​	抽象工厂模式(Abstract Factory)提供一个创建一系列相关或相互依赖对象的接口，而无需指定它们具体的类。

[抽象工厂模式实现](https://github.com/DepInjoy/BaseHouse/blob/master/DesignPattern/%E5%B7%A5%E5%8E%82%E6%A8%A1%E5%BC%8F/%E6%8A%BD%E8%B1%A1%E5%B7%A5%E5%8E%82%E6%A8%A1%E5%BC%8F-%E6%95%B0%E6%8D%AE%E5%BA%93%E6%8E%A5%E5%85%A5.cpp)



#### 优点(Why)

- **易于交换产品系列**。很好地利用了开放封闭原则和依赖倒转原则。

- **使得具体的创建实例过程与客户端分离，**客户端通过它们的抽象接口操纵实例，产品的具体类名也被具体工厂实现分离，不会出现在客户端代码中。



### 模式改进



#### 简单工厂模式来改进抽象工厂

```C++
class DataAccess
{
public:
	User* createUser(void){
		User* result = NULL;
		switch (_dataAccessType)
		{
		case SQL_TYPE:
			result = new SQLUser();
			break;
		case ACCESS_TYPE:
			result = new AccessUser();
			break;
		default:
			break;
		}
		return result;
	}
private:
	typedef enum{
		SQL_TYPE,
		ACCESS_TYPE,
	}__dataAccessType;

	__dataAccessType _dataAccessType = SQL_TYPE;
};
```

这样对客户端隐藏了SQL、Access相关字样，并达到了解耦合的目的。客户端实现代码：

```C++
int main(int argc, char* argv[])
{

	DataAccess* tmp = new DataAccess();
	User* user0 = tmp->createUser();
	User* user1 = new SQLUser();
	user0->getUser(8);
	user0->insertUser(user1);

	delete user0;
	delete user1;
	delete tmp;
	
	return 0;
}
```



#### 反射+抽象工厂数据访问程序

​	利用反射技术可以去除switch或者if判断来基础耦合，由于C++不支持反射技术，详细的实现示例，暂且搁置。



#### 反射+配置文件实现数据访问程序

​	利用配置文件解决获取数据库类型问题。同样地，由于C++不支持反射技术，详细的实现示例，暂且搁置。