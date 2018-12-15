---
layout: post
title: 模板
date: 2018-03-26 20:00
category: CPP
tags: [CPP]
description: 以实例的形式描述对C++模板的理解。
---

## 函数模板

首先看下面的一个实例，实现查找不同类型（int, double等）的列表中的最大数。使用模板可以避免根据数据参数的类型编写很多函数。
```C++
#include <iostream>
using namespace std;
template<class T>
T max(T a[], int size) {
	T max = a[0];
	for (int i = 0; i < size; i++) {
		if (max < a[i])
			max = a[i];
	}
	return max;
}

int main(void)
{
	int a[5] = {2, 45, 6, 78, 29};
	cout << max(a, 5) << endl;		//78

	double b[8] = {3.4, 6.7, 1.2, 8,9, 120, 34.89};
	cout << max(b, 8) << endl;		//120
	return 0;
}
```
### 不进行模板实例化
```C++
#include <iostream>
using namespace std;

template<class T>
T inc(T n){
	return n + 1;
}

int main(void)
{
	int n = 8;
	cout << inc<double>(n) / 2 << endl;	//4.5
	cout << inc(n) / 2 << endl;		    //4
	cout << inc(8) / 2 << endl;			//4
	return 0;
}
```
### 匹配函数模板时不自动进行类型匹配
```C++
#include <iostream>
using namespace std;
template<class T>
T max(T a, T b) {
	T max = (a > b) ? a : b;
	cout << "template max " << max << endl;
	return max;
}
int main()
{
	int a = 6, b = 8;
	double c = 9.8, d = 10.2;
	max(a, c);
	return 0;
}
```
上面的代码编译时会给出下面的编译错误信息：
```
error C2784: “T max(T,T)”: 未能从“double”为“T”推导 模板 参数
```
在上面的代码中添加如下的模板函数就可以解决：
```C++
template<class T, class T1>
T max(T a, T1 b) {
	T max = (a > b) ? a : b;
	cout << "template max max " << max << endl;
	return max;
}
```
### 函数模板和函数的调用次序
如果有多个函数和函数模板名相同情况下，编译器进行如下函数调用语句：
- 1 先找参数完全匹配的普通函数(非模板实例化等到的)，参看实例的max(a, b)调用。
- 2 再找参数完全匹配的模板函数，参看下面示例的max(a, c);max(c, d);调用
- 3 再找实参经过自动类型转化能够匹配的普通函数(非模板实例化等到的)
- 4 如果上面都找不到，则报错。参看标题匹配函数模板时不自动进行类型匹配的示例。

```C++
#include <iostream>
using namespace std;
template<class T>
T max(T a, T b) {
	T max = (a > b) ? a : b;
	cout << "template max " << max << endl;
	return max;
}

template<class T, class T1>
T max(T a, T1 b) {
	T max = (a > b) ? a : b;
	cout << "template max max " << max << endl;
	return max;
}

int max(int a, int b) {
	int max = (a > b) ? a : b;
	cout << "cal max of int " << max << endl;
	return max;
}

int main()
{
	int a = 6, b = 8;
	double c = 9.8, d = 10.2;
	max(a, b);	//cal max of int 8
	max(a, c);	//template max max 9
	max(c, d);	//template max 10.2
	return 0;
}
```