---
layout: post
title: 桶排序算法
date: 2018-1-25 18:04
category: 算法
tags: [算法]
description: 
---

```C++
#include <iostream>
using namespace std;
#define MAX_NUM 32
/*
	为了表述算法复杂度，假设桶的个数为m，待排序数的个数为n
	总循环次数为m+n+m+n=2(m+n),时间复杂度为O(2(m+n))，忽略数字2则为O(m+n)
*/
void bucketSort(int* data, size_t size)
{
	int bucket[MAX_NUM] = { 0 };
    for (int i = 0; i < MAX_NUM; i++){		//循环m次
		bucket[i] = 0;
	}
	for (int i = 0; i < size; i++){			//循环n次
		int value = data[i];
		bucket[value]++;
	}
	for (int i = 0; i < MAX_NUM; i++){		//循环m+n次
		for (int j = 0; j < bucket[i]; j++)	
			cout << " " << i;
	}
}

int main(int argc, char* argv[])
{
	int tmp[6] = {3,6,2,1,9,20};
	bucketSort(tmp, 6);				//1 2 3 6 9 20
	return 0;
}
```

​	该算法的时间复杂度为o(M+N)，其中M为桶的个数，N为待排序数的个数，该算法的缺点在于只可以应用于整数，无法处理处理小数，而且浪费空间(桶的个数必须大于等于待排序数的最大值)。