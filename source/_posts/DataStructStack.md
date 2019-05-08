---

layout: post
title: 栈
date: 2019-5-8 10:32
category: 数据结构
tags: [数据结构]
description: 
---

### [栈](http://www.cplusplus.com/reference/stack/stack/)

​	C++中stack定义在```#include<stack>```,是一种LIFO的容器适配器。**默认是基于deque实现的，也可以基于list、vector实现**。

```C++
template <class T, class Container = deque<T> > class stack;
```

```C++
//stk基于vector实现的空栈，修改了存储容器的类型为vector
std::stack<std::string, vector<std::string> > stk;
```

其中，常用操作，参看《C++ Primer》P330



### 训练题

#### [括号有效性](https://leetcode-cn.com/problems/valid-parentheses/)

**实现思想**：

```C++
检测(, [, { 到直接入栈
检测到）,], }则判断栈顶是否是对应的(, [, { ，不是则返回false，否则将栈顶元素弹出。
```

[LeetCode实现代码](https://github.com/DepInjoy/BaseHouse/blob/master/Interview/%E6%A0%88/%E5%88%A4%E6%96%AD%E6%8B%AC%E5%8F%B7%E6%9C%89%E6%95%88%E6%80%A7.cpp)



#### [栈实现队列](https://leetcode-cn.com/problems/implement-queue-using-stacks/description/)

**实现思路**：用两个栈来实现，用一组数据描述如下：

````C++
队列：1 2 3 4 5

栈1:1 2 3 4 5
栈2:5 4 3 2 1

从栈2取出元素的顺序为：1 2 3 4 5和队列是一致的。
````

[LeetCode实现代码](https://github.com/DepInjoy/BaseHouse/blob/master/Interview/%E6%A0%88/%E6%A0%88%E5%AE%9E%E7%8E%B0%E9%98%9F%E5%88%97.cpp)