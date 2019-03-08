---
layout: post
title: C++基础
date: 2018-1-8 18:04
category: CPP
tags: [CCPP]
description: 介绍一些C语言基础知识。
    1、x++和++x、x--和--x的区别
    2、运算符优先级
---



​	C++语言属于静态数据类型语言，在编译时进行数据类型检查，在编译时就必须知道数据的类型。可以在运行时通过typeid运算符返回的type_info类型的对象。

```C++
#include <iostream>
int main(int argc, char* argv[])
{
	int a = 0;
	std::cout << typeid(a).name() << std::endl;
	return 0;
}
```



C++语言优势：

- 便于软件重用。

  C++软件重用主要体现在以下几个方面：

  1. 面向对象的思想：继承、多态、标准类库。
  2. 泛型编程思想：模板机制和标准模板库STL。

#### x++和++x、x--和--x的区别

x++是先对x执行运算，再对x自增1，++x是先对x自增1，再对x执行运算。同样地，x--是先对x执行运算，再对x自减1，--x是先对x自减1，再对x执行运算。

```C
#include <stdlib.h>
#include <stdio.h>
int main(int argc, char* argv[])
{
	int i = 2; 
	printf("%d %d\n", i, ++i);			//3 3
	
    i = 2;
	printf("%d %d\n", i, i++);			//3 2
	
    i = 2;
	i = (++i) + (++i) + (++i) + (++i);
	printf("%d n", i);					//24
    
    i = 2;
	i = (i++) + (i++) + (i++) + (i++);
	printf("%d\n", i);					//12
	return 0;
}
```



```C
#include <stdlib.h>
#include <stdio.h>
int main(int argc, char* argv[])
{
	int x = 5;
	//先判断x<6,再执行x=x+1
	if (x++ < 6){
		printf("Before %d \n", x--);	//6
	}else{
        //先打印x的值，再执行x = x -1
		printf("After %d \n", x--);
	}
	printf("Last %d \n", x);			//5

	x = 5;
	//先执行x=x+1，再判断x<6,
	if (++x < 6){
		printf("Before %d \n", x--);
	}else{
		printf("After %d \n", x--);		//6
	}
	return 0;
}
```

##### 运算符优先级

