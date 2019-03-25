---
layout: post
title: 查找算法
date: 2017-1-26 18:04
category:算法
tags: [算法]
description: 
---

​	

### 二分查找

​	二分查找算法在一组已经排序好的数组中搜索特定元素的算法，从中间元素开始搜索，如果中间元素小于被搜索元素，则将搜索范围缩小为大于中间元素的那一部分，依次循环直至找到相关元素，否则则找不到相关元素。该算法可以实现每次搜索将搜索范围减少一半。

```C++
#include <iostream>
using namespace std;
/*
	实现在list中从序列号low到high查找data
	
	@param low:		查找序列号最小值
	@param high:	查找序列号最大值
	@param data:	欲查找的数值
	@param list:	查找序列
	
	@return			欲查找数值所在序列的标号
*/
template<class T>
int binarySearch(int low, int high, T data, T* list){
	if (data == list[low]){
		//cout << "param(" << data << ") found. at " << low << endl;
		return low;
	}	
	while (low < high){
		int mid = (low + high + 1) / 2;
		if (data > list[mid]){
			low = mid;
			continue;
		}else if(data < list[mid]){
			high = mid - 1;
			continue;
		}
		//cout << "param(" << data << ") found. at "<< mid << endl;
		return mid;
	}
	//cout << "param(" << data << ") not found!" << endl;
	return -1;
}

int main(int argc, char* argv[])
{
	//测试用例
	int param[10] = { 3, 5, 8, 10, 15, 19, 20, 23, 45, 78 };
	int pos = binarySearch(0, 9, 78, param); //9
	pos = binarySearch(0, 9, 12, param);	//-1
	pos = binarySearch(0, 9, 3, param);		//0
	pos = binarySearch(1, 9, 15, param);    //5

	cout << "------------------------------------------------" << endl;
	float param2[10] = { 3.12, 5.4, 8.6, 10.3, 12.67, 15.55, 19.9, 20, 23, 78.6 };
	float data = 78.6;
	pos = binarySearch(0, 9, data, param2);		//9
	data = 12;
	pos = binarySearch(0, 9, data, param2);		//-1
	data = 3.12;
	pos = binarySearch(0, 9, data, param2);		//0
	data = 15.55;
	pos = binarySearch(1, 9, data, param2);		//5
	
    return 0;
}
```

该算法的时间复杂度为O(logN)，其中N我数组元素个数。空间复杂度为O(1)。