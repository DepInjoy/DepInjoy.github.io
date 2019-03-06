---
layout: post
title: C++11新特性--正则表达式
date: 2019-3-6 18:35
category: CPP
tags: [CPP]
description: 
---

[博客](http://blog.topspeedsnail.com/archives/6810)

### 算法

```C++
regex_match();				 //判读是否匹配
regex_search()：				//获得匹配的字符串
regex_replace()：			//替换匹配的字符串
```



```C++
#include <iostream>
#include <regex>

int main(int argc, char* argv[])
{
	std::string str = "My first program is Hello World! oh Yeah";

	std::regex match("(.*)(Hel)(.*)");
	if (std::regex_match(str, match))
		std::cout << "matched " << std::endl;
	else
		std::cout << "unmatched " << std::endl;
	
	if (std::regex_match(str.begin(), str.end(), match))
		std::cout << "matched " << std::endl;
	else
		std::cout << "unmatched " << std::endl;

	std::regex s("Hel[a-zA-Z]+");
	std::smatch m;  // 存放匹配的字符串
	std::regex_search(str, m, s);
	for (auto x : m)
		std::cout << x << std::endl;	//Hello

    //My first program is Fuck World! oh Yeah
	std::regex r("Hel[a-zA-z]+");
	std::cout << std::regex_replace(str, r, "Fuck") << std::endl;

    //My first program is Hello World! oh Yeah
	std::string res;
	std::regex_replace(std::back_inserter(res), str.begin(), str.end(), r, "Hello");
	std::cout << res << std::endl;
	return 0;
}
```





