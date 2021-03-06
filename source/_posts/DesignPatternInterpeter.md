---
layout: post
title: 设计模式：解释器模式
date: 2019-6-11 21：06
category: 设计模式
tags: [设计模式]
description: 解释器模式是，给定一个语言，定义他们的文法的一种表示，并定义一个解释器，这个解释器使用该表达式来解释语言中的句子。
---

### 解释器模式

​	解释器模式是，给定一个语言，定义他们的文法的一种表示，并定义一个解释器，这个解释器使用该表达式来解释语言中的句子。

[解释器实现代码模板](<https://github.com/DepInjoy/BaseHouse/blob/master/DesignPattern/%E8%A7%A3%E9%87%8A%E5%99%A8%E6%A8%A1%E5%BC%8F/%E8%A7%A3%E9%87%8A%E5%99%A8%E6%A8%A1%E5%BC%8F%E4%BB%A3%E7%A0%81%E6%A8%A1%E6%9D%BF.cpp>) 



#### 适用场景

- 如果一种特定类型的问题发生的频率足够高，那么可能就指的将该问题的各个实例表述为一个简单语言中的句子，构成一个解释器，该解释器通过解释这些句子来解决问题。
- 当有一个语言需要解释执行，并且你可将该语言中的句子表示为一个抽象语法树时，可使用解释器模式。



#### 优点

- **可以容易地改变和扩展文法**，因为该模式使用类来表示文法规则，可用继承来改变或者扩展该文法。也比较容易实现文法，因为定义抽象语法树中各个节点的类的实现大体类似，这些类都易于直接编写。


#### 缺点

- 解释器为每一条规则至少定义了一个类，因此包含许多规则的文法可能难以维护和管理。当文法非常复杂时，使用其他技术如语法分析程序或编译器声称器来处理。