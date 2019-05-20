---
layout: post
title: 设计模式：模板方法模式
date: 2019-5-20 11:14
category: 设计模式
tags: [设计模式]
description: 简单介绍一下模板方法模式，并以答选择题为例实现一个示例程序。
---



### 模板方法模式

​	**模板方法模式定义了一个操作中的算法骨架，将一些步骤延迟到子类中。模板方法使得子类可以不改变一个算法的结构即可重定义该算法的某些特定步骤。**

[模板方法模式：实现做选择题](https://github.com/DepInjoy/BaseHouse/blob/master/DesignPattern/%E6%A8%A1%E6%9D%BF%E6%96%B9%E6%B3%95%E6%A8%A1%E5%BC%8F/%E6%A8%A1%E6%9D%BF%E6%96%B9%E6%B3%95%E6%96%B9%E5%BC%8F-%E7%AD%94%E9%80%89%E6%8B%A9%E9%A2%98.cpp)    [模板方法模式实现代码模板](https://github.com/DepInjoy/BaseHouse/blob/master/DesignPattern/%E6%A8%A1%E6%9D%BF%E6%96%B9%E6%B3%95%E6%A8%A1%E5%BC%8F/%E6%A8%A1%E6%9D%BF%E6%96%B9%E6%B3%95%E6%96%B9%E5%BC%8F%E4%BB%A3%E7%A0%81%E6%A8%A1%E6%9D%BF.cpp)



#### 优点(why)

- 模板方法将不变的行为搬移到超类，去除子类中重复代码，提供了一个很好的代码复用平台