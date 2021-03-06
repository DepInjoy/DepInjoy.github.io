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

​	

​	**C++语言属于静态数据类型语言，在编译时进行数据类型检查，**在编译时就必须知道数据的类型。可以在运行时通过typeid运算符返回的type_info类型的对象。

```C++
#include <iostream>
int main(int argc, char* argv[])
{
	int a = 0;
	std::cout << typeid(a).name() << std::endl;			//int
	return 0;
}
```

程序编译

```C++
		     第三方lib ---------
		     				   |---> 链接器 -->可执行文件
.cpp文件  --> object文件--------   
```



### 程序结构

C++程序主要由两部分组成：

- **预处理程序指令。**如```#include <iostream>```,其中```<>```表示在标准库查找库文件。```""```表示首先在当前目录下查找库文件，如果查找不到则到存储标准库的目录下查找。

- **主函数**。```main()```

```C++
/*
	#:						 Hash标志
	include：				预处理指令
	iostream：				库文件
*/
#include <iostream>
int main(){
	std::cout << "Hello World " << std::endl;
	return 0;
}
//编译指令：gcc main.cpp -o main.out
```

​	C++编译器对类的处理包含两步：**编译成员声明；之后成员函数体(如果存在)。**



### 优/劣势

#### 优势

##### 1、便于软件重用。C++软件重用主要体现在以下几个方面：

- **面向对象的思想**：继承、多态、标准类库。

- **泛型编程思想**：模板机制和标准模板库STL。



### 编程入门

#### 面向对象编程和面向过程编程的区别

```C++
#include <iostream>
class Circle
{
public:
	double r;
	double pi = 3.1415926;
	double s = pi * r * r;
	double getArea(void){ return pi * r * r; }
};

int main(int argc, char *argv[])
{
	Circle circle;
	double tmp = circle.r;
	std::cout << "Before change " << std::endl;
	std::cout << "Area is " << circle.pi * tmp * tmp << std::endl << std::endl;
	circle.r = 10;
	std::cout << "After Change " << std::endl;
	std::cout << "Area is " << circle.s << std::endl;			//random value
	std::cout << "Area is " << circle.getArea() << std::endl;	//right value
	system("pause");
	return 0;
}
```

运行结果：

```C++
Before change
Area is 2.69149e+124

After Change
Area is 2.69149e+124
Area is 314.159
```



#### 编码风格

- [微软编码风格指南](https://github.com/Microsoft/AirSim/blob/master/docs/coding_guidelines.md)

- [Google编码风格指南-英文版](https://google.github.io/styleguide/cppguide.html)

- [Google编码风格指南-中文版](https://zh-google-styleguide.readthedocs.io/en/latest/google-cpp-styleguide/)



#### [算术运算符](http://www.cplusplus.com/doc/tutorial/operators/)

##### [前缀/后缀递增/减运算符](https://en.cppreference.com/w/cpp/language/operator_incdec)

前缀自增/减运算符：先将对象的值增加/减少，然后返回引用。

后缀自增/减运算符：首先，复制对象并创建对象的一个临时副本；然后将对象的值自增/减；最后返回副本。在C++中临时变量是右值，右值不可以进行取地址操作。



###### x++和++x、x--和--x的区别

​	x++是先对x执行运算，再对x自增1，++x是先对x自增1，再对x执行运算。同样地，x--是先对x执行运算，再对x自减1，--x是先对x自减1，再对x执行运算。

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
	printf("%d %d\n", i++, i);			//2,3
    
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

##### [数组Array](https://www.tutorialspoint.com/cplusplus/cpp_arrays.htm)



### 初始化的方式

#### 拷贝初始化

​	使用=去初始化一个变量，此时编译器会将等号右边的值拷贝给新创建的对象中。

```C++
std::string s = "Hiya";
```

​	对于多个值也可以使用拷贝初始化。

```c++
/*
	实际执行过程：
	std::string temp = std::string(3, 'H');
	std::string s0 = temp;
*/
std::string s0 = std::string(3, 'H');		//HHH
```



#### 直接初始化

​	不使用=，则执行的是直接初始化。

```C++
std::string s("Hiya");		//Hiya
std::string s0(3, 'H');		//HHH
```








