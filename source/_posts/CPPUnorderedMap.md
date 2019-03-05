---
layout: post
title: C++11新特性--无序容器(哈希表)
date: 2019-3-5 18:04
category: CPP
tags: [CPP]
description: 本文介绍了无序容器的特点以及使用场景，并给除了使用示例实现数据查询和遍历。
---



## 无序容器

​	**无序容器对应的数据结构是哈希表，其特点是查找效率高**，查找的时间复杂度为O(1)，而额外空间复杂度则要高出很多，因此无序容器在需要高效率查询时，可以使用unordered_map容器，如果堆内存大小敏感或堆数据存储要求有序时，则可以考虑使用map容器。



使用示例：

```C++
#include <iostream>
#include <unordered_map>
#include <string>

int main(int argc, char **argv) {
	std::unordered_map<int, std::string> map;
	map.insert(std::make_pair(1, "Scala"));
	map.insert(std::make_pair(2, "Haskell"));
	map.insert(std::make_pair(3, "C++"));
	map.insert(std::make_pair(6, "Java"));
	map.insert(std::make_pair(14, "Erlang"));
	std::unordered_map<int, std::string>::iterator it;
	if ((it = map.find(6)) != map.end()) {
		std::cout << it->second << std::endl;
	}
	for (auto i : map){
		std::cout << i.first << " : " << i.second << std::endl;
	}
	return 0;
}
```

