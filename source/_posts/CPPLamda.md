---
layout: post
title: 新特性--Lamda表达式
date: 2019-3-7 7:30
category: CPP
tags: [CPP]
description: 
---





```C++
bool cmp(int a, int b){
	return  a < b;
}
sort(vec.begin(), vec.end(), cmp);
for (int it : vec)
		cout << it << ' ';
```

等价于：

```C++
	//利用Lamda表达式实现vec按照大小顺序进行排序
	sort(vec.begin(), vec.end(), [](int a, int b) -> bool { return a < b; });
	//利用Lamda进行vec遍历并打印
	for_each(lbvec.begin(), lbvec.end(), [](auto x){ cout << x << " "; });
```



实现斐波那契数列

```C++
#include <functional>
//std::function <int(int)>表示入参为int类型，返回值也为int
std::function <int(int)> flib = [&flib](int n) {
	return n <= 2 ? 1 : flib(n - 2) + flib(n - 1);
};
```

