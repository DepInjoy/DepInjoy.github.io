---
layout: post
title: C语言的const修饰符
date: 2017-3-16 18:04
category: C
tags: [C]
description: 本文描述了const char* 、char *const以及const char **和char * const *的区别。
---



根据ANSI C标准的赋值约束条件：
- 两个操作数都是指向有限定符或无限定符的相应类型的指针。
- 左边指针所指向的类型必须具有右边指针所指向类型的全部限定符。因而:
``` c
const char * p = "hello";
char *tmp = "change";
p = tmp;
```
编译时会出现warning: assignment discards ‘const’ qualifier from pointer target type [enabled by default]
```
const char * p = "hello";
char *tmp = "change";
p = tmp;
```
这样的编译是不会出现警告信息的。那么const修饰符有什么作用，以及当其修饰指针时其含义有何不同。

​	const表示的变量是只读属性，const char \*，const \* char定义的变量前者表示指针只读不可更改，后者表示指针指向的内容只读不可更改，方便的记忆方法是将 * 读为　pointer to,然后从后向前读。例如

- char * const cp; //cp is a const pointer to char
- const char * p; 　//p is a pointer to const char;
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

void foo(const char **pp)
{
//*(*pp + 1u) is read-only

*pp=NULL;                                       //allowed
*pp="Hello world!";                             //allowed
*pp = (char *) malloc(10);
snprintf((char *)*pp, 10, "hi google!");
//(*pp)[1] = 'x';                               //*(*pp + 1u) is read-only
//free((char *)*pp);
}   

int main(int argc,char *argv[])
{   
const char *p="hello";
printf("before foo %s\n",p);
foo(&p);
printf("after foo %s\n",p);

const char p0 = 'a';                            //p is read-only
printf("p0 before change value %c\n",p0);
//p = 'b';
//printf("p0 after change value %c\n",p0);

const char *p1 = "hello";                       //*(p + 1u) is read-only
printf("p1 before change %s\n",p1);
char *tmp1 = "change";
p1 = tmp1;
printf("p1 after change %s\n",p1);
//p1[1] = 'x';

char * const p2 = "hello";                      // p is read-only
printf("before change %s\n",p2);
char *tmp2 = "change";
//p2 = tmp2;
//printf("const value after change %s\n",p2);

#if 0
char * const * p3;                              // *p3 read-only location
*p3 = NULL;
char *tmp = "hello";
*p3 = tmp;
printf("change %s %s\n",tmp,*p3);
#endif

return 0;
}   

```
经过上面的测试我们可以看到：
```
const char p;  //p is read-only
char * const p2 = "hello"; // p2 is read-only
const char *p="hello"; //*(p + 1u) is read-only
const char **pp; //*(*pp + 1u) is read-only
char * const * p3;// *p3 read-only location
```



