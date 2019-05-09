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



#### [C++双端队列](http://www.cplusplus.com/reference/deque/deque/)

```C++
template < class T, class Alloc = allocator<T> > class deque;
```



#### [C++优先队列](https://en.cppreference.com/w/cpp/container/priority_queue)

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

[LeetCode C++ 实现代码](https://github.com/DepInjoy/BaseHouse/blob/master/Interview/%E9%98%9F%E5%88%97/%E9%98%9F%E5%88%97%E5%AE%9E%E7%8E%B0%E6%A0%88.cpp)



#### [返回输入流中第K大的元素](https://leetcode-cn.com/problems/kth-largest-element-in-a-stream/)

**实现思路**：

- 保存K个最大值，然后对这K个元素进行排序（快排最快），对应的时间复杂度为N*KlogK。

- 利用优先队列的Min Heap，且该Min Heap的size为K。对应的时间复杂度为N * (1或logK）即N*logK。

```
[ 4,5,8,2,3,9,1]
	[4,5,8]
	   [4,5,8]
	   	  [4,5,8]			  O[1]
	   	  	 [5,8,9]		  O[log(K)]     
	   	  	 	[5,8,9]
```

[LeetCode C++实现代码](https://github.com/DepInjoy/BaseHouse/blob/master/Interview/%E9%98%9F%E5%88%97/%E8%BF%94%E5%9B%9E%E8%BE%93%E5%85%A5%E6%B5%81%E4%B8%AD%E7%AC%ACK%E5%A4%A7%E5%85%83%E7%B4%A0.cpp)



#### [返回滑动窗口中的最大值](https://leetcode-cn.com/problems/sliding-window-maximum/)

**实现思路**：

- 思路一：
  ​	利用MaxHeap，维护堆（加入新元素，删除旧元素）；将对顶元素加入队列
- 思路二：
  ​	利用Queue，实现入队和队列维护。
  ​	把遍历Index视作每次窗口的最后一个index，而当windows中第一个数的index超出窗口范围则弹出(队列存放index)。
  ​        滑动窗口最前方Index对应的数字为val一直是最大值，窗口内元素按递减排序；当检索下一个数时，对应把位于滑动窗口尾部Index对应的数字小于本数值的Index弹出，保证队列数字顺序。
  ​    如果val到达窗口边界后，每次移动都输出对列头元素对应的数。

[LeetCode C++实现代码](https://github.com/DepInjoy/BaseHouse/blob/master/Interview/%E9%98%9F%E5%88%97/%E6%BB%91%E5%8A%A8%E7%AA%97%E5%8F%A3%E6%9C%80%E5%A4%A7%E5%80%BC.cpp)





