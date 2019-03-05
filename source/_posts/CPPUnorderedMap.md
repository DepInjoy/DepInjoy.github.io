---
layout: post
title: C++11新特性--无序容器(哈希表)
date: 2019-3-5 18:04
category: CPP
tags: [CPP]
description: 
---

[无序容器](https://www.sczyh30.com/posts/C-C/cpp-stl-hashmap/)

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

