---
layout: post
title: 设计模式：状态模式
date: 2019-5-28 21：06
category: 设计模式
tags: [设计模式]
description: 状态模式，当一个对象的内在状态改变时，允许改变其行为，这个对象看起来像是改变了其类。
---



### 状态模式

​	**状态模式，当一个对象的内在状态改变时，允许改变其行为，这个对象看起来像是改变了其类。**通过将特定状态相关的行为都放入到一个对象中，由于所有与状态相关的代码都存在于某个ConcreteState中，所有通过定义新的子类可以很容易地增加新的状态和转换，最终消除庞大的条件分支语句。



#### 优点

- 将于特定状态相关的行为局部化，并且将不同状态的行为分隔开来。



#### 使用场景

- 一个对象行为取决于它的状态，且必须在运行时根据状态改变它的行为时。
- 控制一个对象状态转换的条件表达式过于复杂时，把状态判断逻辑转移到表示不同状态的一系列类当中，把复杂的判断逻辑简单化。