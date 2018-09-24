---
layout: post
title: static修饰符
date: 2018-03-20 20:00
category: CPP
tags: [CPP, C]
description: static修饰的变量是静态全局变量，在C++中静态变量不属于任何的类，只是在某个类中进行声明，被所有的类共享。而在C中static变量存储于BSS段，而实现数据的保存。
---
### C++中静态变量

[消息的分级提示](https://github.com/DepInjoy/BaseHouse/blob/master/Qt/GradedMessagePrompt/src/Bubble.cpp)实现在窗口的右下角显示进行消息的提示。该功能中每次弹出新消息需要知道上一个提示窗的y，通过定义一个静态变量
```
static int totalHeight;         //记录当前窗口相对于第一个窗口的高度信息
```
该变量虽在Bubble中定义却从不属于任何一个Bubble。
static变量需要在类外进行初始化
```
int Bubble::totalHeight = 0;
```

### C语言静态变量
static变量在C语言中存储于BSS段，初始化为0或者NULL，在程序运行期间不会被重新分配。下面的这个示例正是利用这个特性实现全局变量的保存。
```c
#ifndef __DICHTOMY_H_
#define __DICHTOMY_H_
#include <stdint.h>

struct data{
    uint8_t areaid;
    uint8_t menuid;
};

struct data * get_data(void){
   static struct data  m_data;
   return &m_data;
}
```

```
#include <stdlib.h>
#include <stdio.h>
#include "dichtomy.h"
int main(int argc, char *argv[])
{
    struct data * test = get_data();
    printf("1 : %d %d \n", test->areaid,test->menuid);
    test->areaid = 100;
    test->menuid = 180;
    struct data * test2 = get_data();
    printf("2 : %d %d \n", test2->areaid, test2->menuid);
}
```

运行的结果是:
```
1 : 0 0
2 ： 100 180
```


