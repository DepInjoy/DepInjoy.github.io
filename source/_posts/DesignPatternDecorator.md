---
layout: post
title: 设计模式：装饰者模式
date: 2019-5-16 22：06
category: 设计模式
tags: [设计模式]
description: 装饰者模式是一种为已有功能动态添加更多功能的一种方式。
---



### [装饰者模式](https://design-patterns.readthedocs.io/zh_CN/latest/structural_patterns/decorator.html) 

 	**装饰者模式是一种为已有功能动态添加更多功能的一种方式。当系统需要新功能时，向旧类中添加新的代码时，这些新增加的代码通常装饰原有类的核心职责或者行为，**此时可以考虑使用该设计模式。

[装饰者模式实现代码模板](https://github.com/DepInjoy/BaseHouse/blob/master/DesignPattern/%E8%A3%85%E9%A5%B0%E8%80%85%E6%A8%A1%E5%BC%8F/%E8%A3%85%E9%A5%B0%E8%80%85%E6%A8%A1%E5%BC%8F%E4%BB%A3%E7%A0%81%E6%A8%A1%E6%9D%BF%E7%A4%BA%E4%BE%8B.cpp)



#### 优点(why)

- 有效地把类的核心职责和装饰功能区分开，可以去除相关类中重复的装饰逻辑。
- 通过使用不同的具体装饰类以及这些装饰类的排列组合，可以创造出很多不同行为的组合。可以使用多个具体装饰类来装饰同一对象，得到功能更为强大的对象。

