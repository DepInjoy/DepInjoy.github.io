---
layout: post
title: 设计模式：观察者模式
date: 2019-5-24 18：06
category: 设计模式
tags: [设计模式]
description: 观察者模式，也称Publish/Subscribe模式，定义了一种一对多的依赖关系，让多个观察者对象同时监听一个主体对象。
---



### 观察者模式(https://design-patterns.readthedocs.io/zh_CN/latest/behavioral_patterns/observer.html)

​	观察者模式，也称Publish/Subscribe模式，定义了一种一对多的依赖关系，让多个观察者对象同时监听一个主体对象。这个主题对象在状态发生改变时，会通知所有观察者对象，使它们能够自动更新自己。**观察者模式实质是在解耦和，让耦合的双方都依赖于抽象，而不是依赖于具体，从而使得各自的变化都不会影响另外一边的变化。**

[观察者模式示例代码](https://github.com/DepInjoy/BaseHouse/blob/master/DesignPattern/%E8%A7%82%E5%AF%9F%E8%80%85%E6%A8%A1%E5%BC%8F/%E8%A7%82%E5%AF%9F%E8%80%85%E6%A8%A1%E5%BC%8F%E7%A4%BA%E4%BE%8B%E4%BB%A3%E7%A0%81.cpp)  



#### 优势(why)

- **观察者模式中关键对象是主体Subject和观察者Observer，一个Subject可以有任意多个Observer，一旦Subject发生改变所有的Observer都可以得到通知。Subject发出通知时无需知道谁时它的观察者，任何一个观察者也无需知道其他观察者的存在。**将一个系统分割为一系列相互协作的类有很多不好的副作用（需要维护对象间的一致性）。而我们不希望为了维护一致性使得各类之间耦合，这样会给维护、扩展和宠用带来不便。



#### 何时用(when)

- 当一个对象改变需要同时改变其他对象
- 一个模型有两种方面，其中一方面依赖于另外一方面，此时使用观察者模式可以将这两者封装在独立的对象中使他们各自独立地改变和使用。



#### 实际应用

- 依赖倒转(老板炒股)
- 事件委托实现(VS页面)