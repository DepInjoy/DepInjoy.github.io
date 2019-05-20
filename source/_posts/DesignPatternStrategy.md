---
layout: post
title: 设计模式：策略模式
date: 2019-2-11 22：06
category: 设计模式
tags: [设计模式]
description: 策略模式定义了算法家族并分别封装起来，让他们之间可以相互替换，此模式让算法的变化不会影响算法客户。
---

​	

## [策略模式](https://design-patterns.readthedocs.io/zh_CN/latest/behavioral_patterns/strategy.html)

​	策略模式定义了系列的算法方法，从概念上来看，这些算法完成的都是相同的工作，只是实现方式不同，它可以以相同的方式调用所有的算法，减少各中算法类与使用算法类之间的耦合，使算法独立于使用客户端而变化。

[策略模式实现模板](https://github.com/DepInjoy/BaseHouse/blob/master/DesignPattern/%E7%AD%96%E7%95%A5%E6%A8%A1%E5%BC%8F/%E7%AD%96%E7%95%A5%E6%A8%A1%E5%BC%8F%E5%AE%9E%E7%8E%B0%E6%A8%A1%E6%9D%BF.cpp) [以商场打折为例展示策略模式的使用](https://github.com/DepInjoy/BaseHouse/blob/master/DesignPattern/%E7%AD%96%E7%95%A5%E6%A8%A1%E5%BC%8F/%E7%AD%96%E7%95%A5%E6%A8%A1%E5%BC%8F-%E5%95%86%E5%9C%BA%E6%89%93%E6%8A%98.cpp) 

### 优/劣势

#### 优势

- 策略模式的Strategy类层次为Context定义了一系列的可供重用的算法或行为。继承有助于析取出这些算法中的公共功能。
- **完全符合“开闭原则”。**用户可以在不修改原有系统基础上选择算法或行为，也可以灵活增加新算法或行为。
- **简化了单元测试。**每个算法都有自己的类，可以通过自己的接口单独测试。
- 提供了管理相关的算法族的办法。

#### 劣势

- 客户端必须知道所有的策略类，并自行决定使用哪一个策略类。

#### 使用场景

​	**适用于算法的封装，**它可以用来封装几乎任何类型的规则，在实践中需要在不同时间应用不同的业务规则时，就可以考虑用策略模式。





- 图说设计模式](https://design-patterns.readthedocs.io/zh_CN/latest/index.html)



### 依赖倒转原则

- what:抽象不应该依赖于细节，细节应该依赖于抽象
- how
  - 高层模块不应该依赖于底层细节，两个都应该依赖于抽象
  - 抽象不应该依赖细节，细节应该依赖于抽象。

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

![](..\_img\建造者模式结构图.png)

[建造者模式博客推荐](<https://design-patterns.readthedocs.io/zh_CN/latest/creational_patterns/builder.html>)

- 解释示例：建造小人
- what:将一个复杂对象的构造和它的表示分离，使得同样的构造过程可以建造不同的表示。
- how:
  - 指挥者：控制建造过程，用它来隔离用户与建造过程的关联。
  - 

- when:
  - 主要用于创建一些复杂的对象，这些对象内部构建件的建造顺序通常是稳定的，但对象内部的构建通常面临着复杂的变化。
  - 当创建复杂对象的算法应该独立于该对象的组成部分以及他们的装配方法。
- why:使得建造代码与表示分离，由于建造者隐藏了该产品是如何组装的，所以如果需要改变一个产品内不展示，只需要在定义一个具体的建造者就可以了。



### 观察者模式(Publish/Subscribe模式)

138页，架构代码待实现

![](..\_img\设计模式\观察者模式.png)

- why:将一个系统分割为一系列相互协作的类有很多不好的副作用（需要维护对象间的一致性）。而我们不希望为了维护一致性使得各类之间耦合，这样会给维护、扩展和宠用带来不便。观察者模式中关键对象是主体Subject和观察者Observer，一个Subject可以有任意多个Observer，一旦Subject发生改变所有的Observer都可以得到通知。Subject发出通知时无需知道谁时它的观察者，任何一个观察者也无需知道其他观察者的存在。
- when:
  - 当一个对象改变需要同时改变其他对象
  - 一个模型有两种方面，其中一方面依赖于另外一方面，此时使用观察者模式可以将这两者封装在独立的对象中使他们各自独立地改变和使用。

- what：观察者模式实际是在解耦和，让耦合的双方都依赖于抽象，而不是依赖于具体，从而使得各自的变化都不会影响另外一边的变化。
- 

实现

- 依赖倒转(老板炒股)
- 事件委托实现(VS页面)
- 