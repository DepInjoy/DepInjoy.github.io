---
layout: post
title: string用法总结
date: 2018-3-18 18:00
category:CPP
tags: [CPP]
description: 
---

​	C++的string类实际上是一个类模板。

```C++
typedef basic_string<char> string
```

​	使用时，需要包含```#include<string>```

## 初始化

- 以字符串直接进行赋值

  ```C++
  string( const char *str );
  ```

- length个ch字符进行赋值

  ```C++
  string( size_type length, char ch );
  ```

- string以索引index开始，长度为length字符拷贝

  ```C++
  string( string &str, size_type index, size_type length );
  ```

- 字符串中以以索引index开始，长度为length字符拷贝

  ```C++
  string( const char *str, size_type length );
  ```

示例：

```C++
#include <iostream>
#include <string>
using namespace std;
int main(int argc, char* argv[])
{
	string str0 = "0 Hello World !";
	string str1("1 Hello World !");
	string str2(8, 'x');					//xxxxxxxx
	string str3(str0, 1, 8);				//Hello Wo
    string str4("Hello World", 2, 8);		//llo Worl
	system("pause");
	return 0;
}
```

## 获取长度

```C++
	string tmp = "Hello World !";
	int length = tmp.length();
	cout << length << endl;
```

## 交换

swap函数实现交换。

```C++
	string tmp0 = "Hello";
	string tmp1 = "Nice to meet you";
	tmp0.swap(tmp1);
	cout << tmp0 << endl;					//Nice to meet you
	cout << tmp1 << endl;					//Hello
```

## 字符查找

- 从指定索引开始正序(从前向后)/反序(从后向前)查找

  - 从string中pos索引开始从前向后查找str，pos的默认值为0，返回值为str第一次出现的位置，如果查找失败则返回string::npos(-1)。

    ```C++
    size_t find (const string& str, size_t pos = 0) const;
    size_t find (const char* s, size_t pos = 0) const;
    ```

  - 从string中pos索引开始从后向前查找str，pos的默认值为0，返回值为str第一次出现的位置，如果查找失败则返回string::npos(-1)。

    ```C++
    int rfind(char c, int pos = npos) const;
    int rfind(const char *s, int pos = npos) const;
    ```

  示例：

  ```C++
  string tmp("Hello Hello !");
  	int res = tmp.find("lo");			//3
  	res = tmp.rfind("lo");				//9
  	res = tmp.find("ll", 1);			//2
  	res = tmp.find("ll", 6);			//8
  ```

- 寻找指定字符串中任意一个字符第一次/最后一次出现的位置

  - 从字符串index索引开始查找中第一个与str中的任意字符匹配的字符，返回它的位置。如果没找到就返回string::npos(-1)。

    ```C++
    size_type find_first_of( const basic_string &str, size_type index = 0 );
    size_type find_first_of( const char *str, size_type index = 0 ); 
    ```

  -  从字符串index索引开始查找中最后一个与str中的任意字符匹配的字符，返回它的位置。如果没找到就返回string::npos(-1)。

    ```C++
    size_type find_last_of( const basic_string &str, size_type index = npos );   size_type find_last_of( const char *str, size_type index = npos );
    ```

    示例：

    ```C++
    	string tmp0("abcde");
    	int res = tmp0.find_first_of("abc");		//0
    	res = tmp0.find_last_of("abc");				//2
    ```

- 寻找指定字符串中任意一个字符第一次/最后一次都不出现的位置

  - 从字符串index索引开始查找中第一个与str中的任意字符都不匹配的字符，返回它的位置。如果没找到就返回string::npos(-1)。

    ```C++
    size_type find_first_not_of( const basic_string &str, size_type index = 0 );   size_type find_first_not_of( const char *str, size_type index = 0 );
    ```

  - 从字符串index索引开始查找中最后与str中的任意字符都不匹配的字符，返回它的位置。如果没找到就返回string::npos(-1)。

    ```
    size_type find_last_not_of( const basic_string &str, size_type index = npos );   size_type find_last_not_of( const char *str, size_type index = npos);
    ```

  示例：

  ```C++
  	int res = tmp0.find_first_not_of("abcde");		//-1
  	res = tmp0.find_first_not_of("ah");				//1
  	res = tmp0.find_last_not_of("abcde");			//-1
  	res = tmp0.find_last_not_of("ah");				//4
  ```

  ## 删除
- erase删除pos索引以及以后的字符

```C++
iterator erase( iterator pos );
```

示例：

```C++
string tmp("Hello Hello !");
tmp.erase(2);	
```





