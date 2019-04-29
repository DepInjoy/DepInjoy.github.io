---
layout: post
title: 线程和线程间同步
date: 2018-3-22 18:04
category: CPP
tags: [CPP]
description: 
---

​	[线程和进程的形象化解释](http://www.ruanyifeng.com/blog/2013/04/processes_and_threads.html)



```C++
#include <iostream>
#include <thread>
class MyThread {
public:
	void operator()() {
		while (true){
			std::cout << "class MyThread " << std::endl;
		}
	}
};

void creatMyThread(void){
	while (true){
		std::cout << "func MyThread " << std::endl;
	}
}

void main(void)
{
	MyThread mt;
	std::thread t1(mt);				//创建线程并执行，要求mt可复制
	std::thread t2(creatMyThread);
	system("pause");
}
```

["互斥锁"](http://zh.wikipedia.org/wiki/%E4%BA%92%E6%96%A5%E9%94%81)（Mutual exclusion，缩写 Mutex），防止多个线程同时读写某一块内存区域。

["信号量"](http://en.wikipedia.org/wiki/Semaphore_(programming))（Semaphore），用来保证多个线程不会互相冲突。



### 死锁

​	当线程A持有独占锁a，并尝试去获取独占锁b的同时，线程B持有独占锁b，并尝试获取独占锁a的情况下，就会发生AB两个线程由于互相持有对方需要的锁，而发生的阻塞现象，称为死锁。

[关于死锁介绍的优秀博客](https://www.jianshu.com/p/26881a1b9e30)

#### 发生死锁的四个必要条件：

1. 互斥条件：一个资源每次只能被一个线程使用。
2. 请求与保持条件：一个线程因请求资源而阻塞时，对方获得的资源保持不放。
3. 不剥夺条件：线程已获得的资源，在未使用完之前，不能强行剥夺。
4. 循环等待条件：若干线程之间形成一种头尾相接的循环等待资源关系。

#### 死锁的处理策略

​	为使系统不发生死锁，必须设法破坏产生死锁的四个必要条件之一，或者允许死锁产生，但当死锁发生时能检测出思索，并有能力实现恢复。一般有如下三种种方法：

1. 死锁预防：破坏导致死锁必要条件中的任意一个就来预防死锁。例如，要求用户申请资源时一次性申请所需要的全部资源，这就破坏了保持和等待条件；将资源分层，得到上一层资源后，才能够申请下一层资源，它破坏了环路等待条件。预防通常会降低系统的效率。
2. 死锁避免：指进程在每次申请资源时判断这些操作是否安全，例如，使用银行家算法。死锁避免算法的执行会增加系统的开销。
3. 死锁检测：死锁预防和避免都是事前措施，而死锁的检测则是判断系统是否处于死锁状态，如果是，则执行死锁解除策略（解除策略的使用的方式就是剥夺。即将某进程所拥有的资源强行收回，分配给其他的进程。）