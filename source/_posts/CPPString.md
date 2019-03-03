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

## 流读取运算

```C++
string tmp;
cin >> tmp;			//输入Hello World并回车，则输入被赋值给tmp

string tmp0;
getline(cin, tmp0);
```

## 赋值

- 通过=和assign进行赋值，两者的不同在于assign可以实现部分赋值。

```C++
	string tmp0("copy"), tmp1, tmp2, tmp3, tmp4;
	tmp1 = tmp0;						//copy
	tmp2.assign(tmp0);					//copy
	
	//从下标索引为1开始赋值2个字符，如果复制长度超过字符串长度则只复制到字符串尾部。
	tmp3.assign(tmp0, 1, 2);			//op
	tmp4.assign(tmp0, 1, 4);			//op
```

- 访问单个字符

  可以通过[]和at()进行访问，两者的不同在于at()会进行范围检查，如果超过范围会给出异常，而[]不进行范围检查，如果超出范围会导致程序crash。但是[]的执行效率会比at()要高一些。

  ```C++
  	string tmp("copy");
  	char c = tmp[0];					//c
  	char c1 = tmp.at(0);				//c
  	
  	//at超出范围异常捕获
  	char c2;			
  	try {
  		c2 = tmp.at(4);
  	}catch (const exception& err) {
  		cout << err.what() << endl;		//invalid string position
  	}
  	//char c3 = tmp[4];					//导致程序crash
  ```

## 连接

+和append都可以实现两个字符串之间的连接，不同在于append可以实现取部分字符实现连接。

```C++
	string tmp0("Hello"), tmp1(" World");
	string tmp = tmp0 + tmp1;						//Hello World

	string tmp2 = tmp0;
	tmp2.append(tmp1);								//Hello World

	//从下标1开始，取2或10个字符，如果字符串内没有足够字符则复制到最后一个字符
	string tmp3, tmp4;
	tmp3.append(tmp0, 1, 2);						//el
	tmp4.append(tmp0, 1, 10);						//ello
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

## 比较

可以使用运算符==、 >、 <、 <=、 >=、 !=以及compare实现。同样compare可以实现部分内容对比。

```

```

## 子串

```C++
string tmp("Hello World!");
//从索引为6的取5个字符
string str = tmp.substr(6, 5)			//World
```





