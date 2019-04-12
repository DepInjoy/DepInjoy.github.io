---
layout: post
title: 高级指针
date: 2017-3-18 18:04
category: C
tags: [C]
description: 本文通过实例的形式展示了指针和二级指针(指针的指针)间操作的不同
---

#### 二级指针
```c
#include <stdlib.h>
#include <stdio.h>

int main(int argc,char *argv[])
{
    int m = 18,n = 28;
    int *mp = &m;
    printf("mp %d %p\tm %d %p\n",*mp,mp,m,&m);
    mp = &n;
    printf("mp %d %p\tm %d %p\tn %d %p\n\n",*mp,mp,m,&m,n,&n);

    int i = 6,j = 8;
    int *ip1 = &i,*ip2 = &j;
    printf("ip1 %d %p\tip2 %d %p\n",*ip1,ip1,*ip2,ip2);
    int **ipip = &ip1;
    printf("ip1 %d %p\tip2 %d %p\tipip %d %p\n",**ipip,*ipip,*ip1,ip1,*ip2,ip2);
    *ipip = ip2;
    printf("ip1 %d %p\tip2 %d %p\tipip %d %p\n",**ipip,*ipip,*ip1,ip1,*ip2,ip2);
    return 0;
}
```
执行的结果如下：
```
mp 18 0x7fff7da43370	m 18 0x7fff7da43370
mp 28 0x7fff7da43374	m 18 0x7fff7da43370	n 28 0x7fff7da43374

ip1 6 0x7fff7da43378	ip2 8 0x7fff7da4337c
ip1 6 0x7fff7da43378	ip2 6 0x7fff7da43378	ipip 8 0x7fff7da4337c
ip1 8 0x7fff7da4337c	ip2 8 0x7fff7da4337c	ipip 8 0x7fff7da4337c
```
从上面的示例可以看到：指针操作只是改变本变量的地址进而使地址中存放的对象改变，而指针的指针的对象操作，导致改变原指向变量的地址和本变量地址，进而导致相应的内容的改变。

