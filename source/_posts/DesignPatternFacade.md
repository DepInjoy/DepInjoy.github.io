---
layout: post
title: 设计模式：外观模式
date: 2019-5-22 18：06
category: 设计模式
tags: [设计模式]
description: 装饰者模式是一种为已有功能动态添加更多功能的一种方式。
---



### [外观模式](https://design-patterns.readthedocs.io/zh_CN/latest/structural_patterns/facade.html)

​	**为子系统中的一组接口提供一个一致的界面，此模式定义了一个高层接口，这个接口使得这个子系统更容易使用。**

[外观模式：股票交易实现](https://github.com/DepInjoy/BaseHouse/blob/master/DesignPattern/%E5%A4%96%E8%A7%82%E6%A8%A1%E5%BC%8F/%E5%A4%96%E8%A7%82%E6%A8%A1%E5%BC%8F-%E8%82%A1%E7%A5%A8%E4%BA%A4%E6%98%93.cpp)

#### 使用场景(When)

- **设计初期，要有意识地将不同的两个层分离。**如经典的三层架构：数据访问层和业务逻辑层，业务逻辑层和表示层，层与层之间建立外观模式。
- 开发阶段，子系统往往由于不断重构演化，变得越来越复杂。外观模式可以提供一个简单接口，减少它们之间的依赖。
- 维护阶段，为新系统开发一个外观Facade类来提供设计粗糙或高度复杂的遗留代码简单的接口，让新系统与Facade对象交互，Facade与遗留代码交互完成所有复杂的工作。