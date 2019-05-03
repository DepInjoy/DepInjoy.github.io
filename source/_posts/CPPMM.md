---
layout: post
title: C++内存管理和动态分配
date: 2017-3-16 18:04
category: CPP
tags: [CPP, C]
description: 本文主要描述C++中内存的申请、释放的方法。
	1、new和malloc的区别
	2、static变量实现的机理。
	3、智能指针
---



我们程序中的变量存储于：

- **静态内存。如局部的static对象，类的static对象以及函数外的变量。**
- **栈：函数内的非static对象。**
- **堆：动态分配的对象。**对于动态分配的对象，如果未及时释放会造成内存泄漏，而在尚有指针引用内存时便释放了该内存，会引起非法内存指针问题。



### 静态变量

​	static修饰的变量是静态全局变量，在C++中静态变量不属于任何的类，只是在某个类中进行声明，被所有的类共享。**局部静态对象在程序第一次经过该对象定义语句时进行初始化，知道程序终止才被销毁**，在次期间，即使对象所在函数执行结束也不会对其有影响。

#### 实际应用

##### 实现一个消息分级提示系统

[消息的分级提示](https://github.com/DepInjoy/BaseHouse/blob/master/Qt/GradedMessagePrompt/src/Bubble.cpp)实现在窗口的右下角显示进行消息的提示。该功能中每次弹出新消息需要知道上一个提示窗的y，通过定义一个静态变量

```C++
static int totalHeight;         //记录当前窗口相对于第一个窗口的高度信息
```

该变量虽在Bubble中定义却从不属于任何一个Bubble。
static变量需要在类外进行初始化

```
int Bubble::totalHeight = 0;
```



##### 保存一个全局变量来便于其他模块调用

​	下面的这个示例正是利用这个特性实现全局变量的保存。

```C
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

```C
#include <stdlib.h>
#include <stdio.h>
#include "dichtomy.h"
int main(int argc, char *argv[])
{
    struct data * test = get_data();
    printf("1 : %d %d \n", test->areaid,test->menuid);		//1 : 0 0
    test->areaid = 100;
    test->menuid = 180;
    struct data * test2 = get_data();
    printf("2 : %d %d \n", test2->areaid, test2->menuid);	//2 ： 100 180
}
```



### 动态内存申请

在实际的使用中，我们可以通过两种方式来申请内存：

- 调用malloc从系统申请制定大小的内存，如果成功则返回指向该由连续字节构成的内存块的指针，如果失败则返回NULL。在不需要再使用时调用free函数将该内存空间释放。同时我们可以通过realloc来调整（增大或减少）内存空间的大小。

  ```C++
  #include <iostream>
  #include <stdlib.h>
  using namespace std;
  /*
  	malloc和remalloc函数实现申请内存、调整（增大、减少）内存示例
  */
  int main(int argc, char* argv[])
  {
  	char* tmp = (char*)malloc(8 * sizeof(char));	//申请8个字节内存空间
  	cout << _msize(tmp) << endl;					//显示指针对应的内控空间大小，8
  	
      tmp = (char*)realloc(tmp, 16 * sizeof(char));	//将原申请的内存空间扩充到16个字节
  	cout << _msize(tmp) << endl;					//16
  
  	tmp = (char*)realloc(tmp, 6 * sizeof(char));	//将原申请的内存空间缩小到6个字节
  	cout << _msize(tmp) << endl;					//6
  	
      free(tmp);									//释放申请的内存空间
  	return 0;
  }
  ```

- 调用new在堆内为结构体分配一块内存空间，在不需要使用时，使用delete释放该内存空间。如果申请失败，则会抛出bad_alloc异常。

  ```C++
  #include <iostream>
  using namespace std;
  /*
  new运算符实现申请内存，remalloc函数实现调整（增大、减少）内存示例
  */
  int main(int argc, char* argv[])
  {
  	char* tmp = NULL;
  	try{
          //申请0x7fffffff个字节就会抛出 bad allocation异常
  		tmp = new char[8];
  	}catch (const std::exception& e){
  		std::cout << e.what() << std::endl;
  	}
  	cout << _msize(tmp) << endl;					//8
  
  	tmp = (char*)realloc(tmp, 16 * sizeof(char));
  	cout << _msize(tmp) << endl;					//16
  
  	tmp = (char*)realloc(tmp, 6 * sizeof(char));
  	cout << _msize(tmp) << endl;					//6
  	delete[]tmp;
  	system("pause");
  	return 0;
  }
  ```

上述两种申请内存的方式，由如下的区别：

- new和delete无需另外包含头文件；malloc需要添加```#include <stdlib.h>```头文件。
- new运算符返回正确类型，在对指针进行赋值时，无需进行类型转化。malloc的返回值是void*，在调用malloc时需要进行显示的类型强制转换成为所需要的指针类型。 
- malloc/free是C++/C标准库函数，new/delete是C++运算符。使用new运算符时自动调用该对象的构造函数，使用delete则自动调用析构函数。而malloc/free是库函数不是运算符，不再编译器控制权限内，无法自动调用构造/析构函数
- malloc需要用户传递欲分配内存的大小参数，而new则会根据传递参数自动计算需要分配的内存的大小，如果希望改变分配内存的大小，只能通过C标准函数malloc、realloc和free来管理大小变化的堆内存块。
- new运算符可以重载，实现自定义内存分配策略，甚至于不分配内存，甚至分配到费内存设备上，而malloc则不可以。

#### 重载new和delete运算符

​	new和delete属于C++中的运算符，我们可以实现对new和delete重载，实现手动管理内存(修改分配和归还工作)。operator new和operator delete只适用于单一对象的内存分配和归还，而Arrays使用的内存由operator new[]分配并由operator delete[]来进行归还。

常常出现下面的情况，我们可能需要对new/delete运算符重载：

- 堆空间已经耗尽，自动开始把某个磁盘文件当虚存储使用
- 用户希望控制某片存储空间的分配。



​	下面介绍一下new/delete运算符重载的实现。重载operator new和operator delete，还需要重载operator new[]和operator delete[]。首先我们来了解一些new/delete运算符的重载规则：

1. 重载operator new的参数个数可以是任意个数，但是第一个参数类型必须是size_t，返回值是void*.
2. 重载operator delete传入的参数类型只能是void。

相关实现的示例代码如下：

##### 局部重载new/delete

​	局部重载new/delete支持将new/delete重载成员函数和友元函数两种形式，相关实现代码如下：

###### 重载为成员函数

​	重载new运算符后，再用new给某个运算符分配空间，在结束调用后使用delete来释放内存空间，实际的调用顺序如下：

1.  new的重载函数
2. 类的构造函数
3. 类的析构函数
4. delete的重载函数

```C++
#include <iostream>
using namespace std;
class A{
public:
	A();
	~A();
	void* operator new(size_t size);
	void operator delete(void* p);
	void* operator new[](size_t size);
	void operator delete[](void*p);
};

A::A(){
	cout << "Construct A " << endl;
}

A::~A(){
	cout << "Deconstruct A " << endl;
}

void* A::operator new(size_t size){
	cout << "A::new " << size << " memmory " << endl;
	return malloc(size);
}

void* A::operator new[](size_t size){
	cout << "A:new[] " << size << " memmory " << endl;
	return operator new(size);
}

void A::operator delete(void* p){
	cout << "delete " << endl;
	free(p);
}

void A::operator delete[](void* p){
	cout << "delete[] " << endl;
	operator delete(p);
}

int main(int argc, char* argv[]){
/*
	A::new 1 memmory
	Construct A
	Deconstruct A
	Free memmory
*/
	A* tmp = new A();
	delete tmp;
	cout << "---------------------------------" << endl;
/*
	A:new[] 6 memmory
	A::new 6 memmory
	Construct A
	Construct A
	Deconstruct A
	Deconstruct A
	delete[]
	delete
*/
	A* tmp0 = new A[2];
	delete[] tmp0;
	cout << "---------------------------------" << endl;

	int* i = new int;
	delete i;
	system("pause");
	return 0;
}
```

sizeof是面试的一个常用考点 

- 上述的```class A```是一个空类型，不包含任何的成员变量和成员函数，该类型的```sizeof```是多少？

  > 1,空类型不包含任何的信息，但是我们在声明时，注定会占用一定的空间，否则无法使用，至于具体占用多少由编译器决定，在VS中，每个孔示例类型占用1字节空间。

- 添加构造和析构函数，再对其取```sizeof```,结果是多少？

  > 1,构造和析构函数只需要知道函数的地址即可，而这些函数的地址只与类型相关，与实例无关，编译器不会对因为这两个函数而在实例内添加任何额外的信息。

- 若将析构函数标记为虚函数，再对其取```sizeof```,结果是多少？

  > 4或8,C++编译器一旦发现类中有虚函数，便会在该类型中生成一个虚函数表，并在该类型的每个实例中添加一个指向虚函数表的指针。在32位机器上，一个指针4个字节，取sizeof得到4；而在64位机器上，一个指针8个字节，取sizeof得到8。

###### 重载为友元函数



##### 全局重载new/delete

​	全局重载new/delete后，C++原定义的new/delete将被忽略，重载实现将应用于所有的类型的内存分配。

```C++
#include <iostream>
using namespace std;
void* operator new(size_t size)
{
	if (!size)	return NULL;
	cout << "new " << size << " memory !" << endl;
	return malloc(size);
}
void operator delete(void *p)
{
	cout << "delete " << endl;
	free(p);
}

void* operator new[](size_t size)
{
	if (!size)	return NULL;
	cout << "new[] " << size << " memory !" << endl;
	return operator new(size);
}
void operator delete[](void* p)
{
	cout << "delete[]" << endl;
	operator delete(p);
}
int main(int argc, char* argv[])
{
	/*
		new 4 memory !
		delete
		---------------------
		new[] 12 memory !
		new 12 memory !
		delete[]
		delete 
	*/
	int* tmp = new int;
	delete tmp;
	cout << "---------------------" << endl;
	int* tmp0 = new int[3];
	delete[] tmp0;
	return 0;
}
```



#### 智能指针

​	为了更安全地使用动态内存，C++新标准引入了智能指针。智能指针也是一种模板，存在两种管理底层指针的方式：

- **shared_ptr：允许多个指针指向同一个对象。**
- **unique_ptr：“独占”锁指向的对象。** 

#### shared_ptr

- shared_ptr对象托管一个new运算符返回的指针，之后无须关心内存释放问题。
- 当多个shared_ptr对象托管一个指针时，系统会维护一个计数器，当无shared_ptr托管该指针（shared_ptr计数器变为0）时，便会delete该指针.
- shared_ptr不能托管指向动态分配的数组指针，否则程序会出错。

```C++
#include <memory>
#include <iostream>
using namespace std;
class A
{
public:
	int n = 0;
	A(int a){ n = a; };
	~A(){ cout << "Destruct A" << endl; };
};

int main(int argc, char* agv[])
{
	//sp1, sp2, sp3都托管A(6)
	shared_ptr<A> sp1(new A(6));	
	shared_ptr<A> sp2(sp1);
	shared_ptr<A> sp3 = sp2;
	cout << sp1->n << " " << sp2->n << " " << sp3->n << endl;		//6 6 6
	A* tmp = sp1.get();
	cout << tmp->n << endl;

	sp1.reset();		//sp1放弃托管A(6)
	if (!sp1) cout << "sp1 = NULL" << endl;

	A* tmp2 = new A(8);
	sp1.reset(tmp2);	//sp1托管A(8)
	cout << sp1->n << endl;										//8
	return 0;
}
```

**测试用例一：**

```C++
上面的程序在程序终止时，自动调用A的析构函数
```

**测试用例二**

```C++
//sp2、sp3都放弃托管，此时指针无托管，调用析构，释放资源
sp2.reset();
sp3.reset();
```


```C++
A * a = new A(6);
shared_ptr<A> sp1(a);
#if 1
	//该操作并不增加shared_ptr对指针的托管，程序由于两次释放A导致crash
	shared_ptr<A> sp2;
	sp2.reset(a);
	std::cout << sp2.use_count() << std::endl;		//1
#else
	//该操作shared_ptr的计数器增加，避免了两次释放A导致crash问题
	shared_ptr<A> sp2(sp1);
	std::cout << sp2.use_count() << std::endl;		//2
#endif
	
```

