---
layout: post
title: 设计模式：策略模式
date: 2019-2-11 22：06
category:设计模式
tags: [设计模式]
description: 策略模式定义了算法家族并分别封装起来，让他们之间可以相互替换，此模式让算法的变化不会影响算法客户。
---

### 模板方法模式

- when:  当我们要完成某一细节层次一致的一个过程或一系列步骤，但某个别步骤在更详细的层次上实现可能不同时，我们通常考虑用模板方法模式来处理。

- why:  定义一个操作中算法骨架，将一些步骤延迟到子类中，模板方法使得子类可以在不改变一个算法结构即可重定义该算法的某些步骤。

- what:模板方法模式通过把不变行为搬移到超类，去除子类中重复代码来体现它的优势，进而提供一个很好的代码复用平台。
- how:当不变的和可变的行为在方法子类中实现中混合在一起时，不变行为就会在子类中重复出现，通过模板方法模式把这些行为搬移到单一地方，这样就帮助子类摆脱重复不变行为的纠缠。

```C++
class AbstractClass
{
public:
	virtual void operationA(void) = 0;
	virtual void operationB(void) { 
		std::cout << "AbstractClass::operationB"<< std::endl; 
	};
};
class ConcreateClassA: public AbstractClass
{
public:
	virtual void operationA(void) {
		std::cout << "ConcreateClassA::operationA" << std::endl;
	}
	virtual void operationB(void) {
		std::cout << "ConcreateClassA::operationB" << std::endl;
	}
};

class ConcreateClassB : public AbstractClass
{
public:
	virtual void operationA(void) {
		std::cout << "ConcreateClassB::operationA" << std::endl;
	}
	virtual void operationB(void) {
		std::cout << "ConcreateClassB::operationB" << std::endl;
	}
};
```



### 埃米特法则(最少只是原则)



### 外观模式(Facade)

- what:为子系统中的一组接口提供一个一致的界面，此模式定义了一个高层接口，这个接口使得这个子系统更容易使用。
- when:
  - 设计初期，要有意识地将不同的两个层分离。如讲点的三层架构：数据访问层和业务逻辑层，业务逻辑层和表示层，层与层之间建立外观模式。
  - 开发阶段，子系统往往由于不断重构演化，变得越来越复杂。外观模式可以提供一个简单接口，减少它们之间的依赖。
  - 维护阶段，为新系统开发一个外观Facade类来提供设计粗糙或高度复杂的遗留代码简单的接口，让新系统与Facade对象交互，Facade与遗留代码交互完成所有复杂的工作。

### 建造者模式

