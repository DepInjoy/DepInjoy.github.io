---
layout: post
title: 函数对象
date: 2018-3-26 18:04
category: CPP
tags: [CPP]
description: 
---

### 函数对象	

​	函数对象是在类的定义中重载()运算符，在对象使用时可以达到和函数调用相同的效果。一个简单实现乘法的函数对象实现如下：

```C++
#include <iostream>
using namespace std;
class A
{
public:
	int operator()(int a, int b);
};
int A::operator()(int a, int b)
{
	return a * b;
}
int main(int argc, char* argv[]){
	A tmp;
	int res = tmp(2, 3);
	cout << res << endl;			//6
	return 0;
}
```

#### 应用实践

##### 结合STL算法模板实现常用的性能不错的算法

​	如下例所示，结合accumulate实现累加功能。

```C++
#include <iostream>
#include <vector>
#include <numeric>
using namespace std;
class A
{
public:
	A(int init){ _init = init; }
	int operator()(int total, int b);
private:
	int _init;
};
int A::operator()(int total, int b)
{
	static bool inited = false;
	if (!inited){
		total += _init;
		inited = true;
	}
	return total + b;
}

int multi(int total, int b){
	return total + b;
}

int main(int argc, char* argv[]){
	vector<int> v;
	v.push_back(1); v.push_back(2); v.push_back(3); v.push_back(4);
	A tmp(0);
	int res = accumulate(v.begin(), v.end(), 10, tmp);					//20
	cout << res << endl;
	res = accumulate(v.begin(), v.end(), 10, multi);					//20
	cout << res << endl;
	return 0;
}
```



