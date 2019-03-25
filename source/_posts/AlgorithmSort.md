---
layout: post
title: 排序算法
date: 2017-1-26 20:04
category:算法
tags: [算法]
description:本文介绍并实现一些排序算法，并介绍和排序算法相关的时间复杂度和优缺点，如冒泡排序、桶排序和快速排序等等。
---



### 冒泡排序

​	冒泡排序的核心思想是，通过双重循环，对比两个相邻元素是否符合预期的排序，如果不合适则交换位置。

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



### 桶排序

```C++
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



### 快速排序

```C++
/*
@data the data to be sorted
@left the position to begin on the left side
@right the position to begin on the right side
*/
void quick_sort(int *data, int left, int right)
{
    if(left > right)
        return;

    int flag = data[left];
    int i = left, j = right;
    int tmp = 0;

    while(i != j){
				/*
					start on the left on the left,then right step out first
					First, find the data on right side which is smaller than flag
				*/
        while(data[j] >= flag && i < j)
            j--;
				/* Second, find the data on left side which is bigger than flag */
        while(data[i] <= flag && i < j)
            i++;
        if(i < j){
            tmp = data[i];
            data[i] = data[j];
            data[j] = tmp;
        }
    }

    data[left] = data[i];
    data[i] = flag;

    quick_sort(data,left,i - 1);
    quick_sort(data,i + 1,right);
}
```

​	该算法的时间复杂度为O(NlogN)，N为待排序数的个数。

