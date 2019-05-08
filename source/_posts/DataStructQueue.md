---
layout: post
title: 队列
date: 2019-5-8 10:32
category: 数据结构
tags: [数据结构]
description: C++中队列包含queue、deque和priority_queue三种。
---

### 队列

#### [C++普通队列](http://www.cplusplus.com/reference/queue/queue/)

​	C++中queue定义在```#include<queue>```文件中，是一种FIFO（先进先出）的容器适配器，**默认基于deque实现的，也可以用list和vector实现**。

```C++
template <class T, class Container = deque<T> > class queue;
```

其中，常用操作，参看《C++ Primer》P330



#### 双端队列



#### [优先队列](https://en.cppreference.com/w/cpp/container/priority_queue)

​	C++的priority_queue定义在```#include<queue>```文件中，**默认基于vector实现，也可以用deque实现，默认使用 < 确定相对优先级，我们可以通过重载实现自定义优先级。**

```C++
template<
    class T,
    class Container = std::vector<T>,
    class Compare = std::less<typename Container::value_type>
> class priority_queue;
```

其中，常用操作，参看《C++ Primer》P330



### 训练题

#### [用队列实现栈](https://leetcode-cn.com/problems/implement-queue-using-stacks/description/)

实现思想：用两个队列进行一下转接。

```C++
入栈：插入数据：1 2 3 4 5   
队列1: 1 2 3 4 5
队列2：空

弹出：
队列1：5，记录该值之后弹出。
队列2：1 2 3 4
```

[LeetCode实现代码](https://github.com/DepInjoy/BaseHouse/blob/master/Interview/%E9%98%9F%E5%88%97/%E9%98%9F%E5%88%97%E5%AE%9E%E7%8E%B0%E6%A0%88.cpp)

