---
layout: post
title: 冒泡排序
date: 2017-1-26 20:04
category:算法
tags: [算法]
description: 
---

冒泡排序的核心思想是，通过双重循环，对比两个相邻元素是否符合预期的排序，如果不合适则交换位置。

```C++
#include <iostream>
using namespace std;
/*
	@param list		待排序列表
	@param len		待排序列表中数的个数
*/
template<class T>
void bubbleSort(T* list, int len){
	for (int i = 0; i < len - 1; i++){
		for (int j = 0; j < len - 1 - i; j++){
			if (list[j] > list[j + 1]){
				T tmp = list[j + 1];
				list[j + 1] = list[j];
				list[j] = tmp;
			}
		}
	}
}

int main(int argc, char* argv[])
{
	//测试用例
	int param[10] = { 3, 5, 8, 10, 15, 19, 20, 23, 45, 78 };
	/* 3 5 8 10 15 19 20 23 45 78*/
    bubbleSort(param, 10);
	for (int i = 0; i < 10; i++)
		cout << " " << param[i];
	cout << endl;
	
	int param1[10] = { 78, 52, 48, 30, 25, 19, 10, 5, 4, 3 };
	/*3 4 5 10 19 25 30 48 52 78*/
    bubbleSort(param1, 10);
	for (int i = 0; i < 10; i++)
		cout << " " << param1[i];
	cout << endl;

	cout << "------------------------------------------------" << endl;
    float param2[10] = { 3.12, 5.4, 8.6, 10.3, 12.67, 15.55, 19.9, 20, 23, 78.6 };
	/*3.12 5.4 8.6 10.3 12.67 15.55 19.9 20 23 78.6*/
    bubbleSort(param2, 10);
	for (int i = 0; i < 10; i++)
		cout << " " << param2[i];
	cout << endl;
	return 0;
}
```

该算法的时间复杂度为O(N<sup>2</sup>)，其中N是待排序的数的个数。该算法的主要缺点是算法的时间复杂度高。