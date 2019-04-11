---

layout: post
title: TCP通信协议
date: 2019-1-18 21:43
category: TCP
tags: [TCP,通信协议]
description:
---

### OSI七层模型

![](..\_img\TCP\OSI七层模型.gif)

### 三次握手和四次挥手

[TCP/IP传输层](https://wjboy49.iteye.com/blog/1041575)

[TCP数据包的结构](https://www.cnblogs.com/the-tops/p/6587498.html)

[通俗大白话来理解TCP协议的三次握手和四次分手](https://github.com/jawil/blog/issues/14#)

[拥塞控制和慢启动](https://blog.csdn.net/wdscq1234/article/details/52517420)

[TCP 的那些事儿](https://coolshell.cn/articles/11609.html)

- 三次握手的原因

  - 引用知乎上的别人引用的一个回答，从另外一个角度阐释：

    > 在Google Groups的TopLanguage中看到一帖讨论TCP“三次握手”觉得很有意思。贴主提出“TCP建立连接为什么是三次握手？”的问题，在众多回复中，有一条回复写道：“这个问题的本质是, 信道不可靠, 但是通信双发需要就某个问题达成一致. 而要解决这个问题, 无论你在消息中包含什么信息, 三次通信是理论上的最小值. 所以三次握手不是TCP本身的要求, 而是为了满足"在不可靠信道上可靠地传输信息"这一需求所导致的. 请注意这里的本质需求,信道不可靠, 数据传输要可靠. 三次达到了, 那后面你想接着握手也好, 发数据也好, 跟进行可靠信息传输的需求就没关系了. 因此,如果信道是可靠的, 即无论什么时候发出消息, 对方一定能收到, 或者你不关心是否要保证对方收到你的消息, 那就能像UDP那样直接发送消息就可以了.”。这可视为对“三次握手”目的的另一种解答思路。

  - 

![](..\_img\TCP\TCP三次握手和四次挥手.jpg)

​											[图片来源](https://github.com/jawil/blog/issues/14#)

### QT TCP连接过程

![](..\_img\QtTCPLink.png)



[Qt TCP连接实现服务器和客户端代码](https://github.com/DepInjoy/BaseHouse/tree/master/Qt/GradedMessagePrompt/src/TCP)



[进程间通信：AF_INET域与AF_UNIX域socket通信原理对比](https://blog.csdn.net/sandware/article/details/40923491)

[Linux常用命令-1](https://segmentfault.com/a/1190000013357151)

[Linux常用命令-2](http://www.cnblogs.com/vamei/archive/2013/03/03/2871198.html)

[linux系统日志以及分析](https://blog.csdn.net/dubendi/article/details/78782691)

