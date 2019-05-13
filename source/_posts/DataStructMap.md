---
layout: post
title: Map
date: 2019-5-13 10:32
category: 数据结构
tags: [数据结构]
description:
---



### 训练题

#### [有效的字母异位词](https://leetcode-cn.com/problems/valid-anagram/description/)

**实现思路**：

- 将字符串按照字典序进行排序，最快的时快排，对应的时间复杂度为O(NlogN)。
- 利用Map进行字符数计数，判断两个map是否一致。对应的时间复杂度为O(N)。

[LeetCode实现代码](https://github.com/DepInjoy/BaseHouse/blob/master/Interview/Map/%E6%9C%89%E6%95%88%E7%9A%84%E5%AD%97%E6%AF%8D%E5%BC%82%E4%BD%8D%E8%AF%8D.cpp)



#### [计算两数之和](https://leetcode-cn.com/problems/two-sum/)

**实现思路**：

- 暴力求解
  ```执行两层for循环，对应的时间复杂度为O(N^2)```

- 利用HashMap进行查找

  ```C++
  假设求x+y=9,则y=9-x。对应的时间复杂度为O(N)。
  		for x (nums[0]->nums[len])			O(N)
  			map.find(9 - x)					O(1)
  ```

[LeetCode实现](https://github.com/DepInjoy/BaseHouse/blob/master/Interview/Map/%E4%B8%A4%E6%95%B0%E4%B9%8B%E5%92%8C.cpp)



#### [三个数之和](https://leetcode-cn.com/problems/3sum/)

- 暴力求解

  进行三个循环，对应的时间复杂度为O(N^3)

- 利用Set进行求解

  ```C++
  for x = (nums[0] -> nums[len])				O(N)
  	for y = (nums[0] -> nums[len])				O(N)
  	z = -(x+y),转化为求两数之和问题。	   	     	O(1)
  ```

- sort-find求解，可以节省空间

  ```C++
  对nums进行排序，最快是快排。					O(NlogN)
  for x = (nums[0] -> nums[len])				O(N)
  	y + z = sum - x;
  	y = nums[0], z = nums[len]
  	根据y+z的和移动y或者z		    		   O(N)
  ```

[LeetCode实现](https://github.com/DepInjoy/BaseHouse/blob/master/Interview/Map/%E4%B8%89%E6%95%B0%E4%B9%8B%E5%92%8C.cpp)



