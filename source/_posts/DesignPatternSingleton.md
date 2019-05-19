---
layout: post
title: 设计模式：单例
date: 2019-4-27 20:04
category: 设计模式
tags: [设计模式]
description: 介绍了单例模式的几种实现方式。
---

​	单例对象的类必须保证只有一个实例存在。实现单例的类能返回对象一个引用和获得该实例的静态方法。同时我们通过将该类的构造函数定义为私有方法，这样其他的代码就无法通过调用该类的构造函数来实例化该类的对象，只有通过该类提供的静态方法来得到该类的唯一实例。

## 单例模式的实现方式

### 懒汉模式

​	也称延迟初始化，在单例即将使用时才进行初始化。

```C++
class Singleton{
public:
	static Singleton* getInstance(){
		if (!Singleton::_instance){
			if (!Singleton::_instance){
				Singleton::_instance = new Singleton();
			}
		}
		return Singleton::_instance;
	}
private:
    //禁止copy
    Singleton(const Singleton& );
    Singleton& operator=(const Singleton& );
	static Singleton* _instance;
};
Singleton* Singleton::_instance = NULL;
```

​	上述实现对于多线程是不安全的，且线程锁是一个十分耗时的操作，因而只在不要的时候进行加锁，来保证线程的安全性。

```C++
#include <iostream>
#include <mutex>
#include <thread>

std::mutex g_mutex;
class Singleton{
public:
	static Singleton* getInstance(){
		if (!Singleton::_instance){
			g_mutex.lock();
			if (!Singleton::_instance){
				Singleton::_instance = new Singleton();
			}
			g_mutex.unlock();
		}
		return Singleton::_instance;
	}
private:
    //禁止copy
    Singleton(const Singleton& );
    Singleton& operator=(const Singleton& );
	static Singleton* _instance;
};
Singleton* Singleton::_instance = NULL;
```



上述方式的实现存在内存泄漏的风险，可以通过两种方式来解决该问题：

1. 使用静态的嵌套类对象。
2. 使用智能指针。

#### 静态的嵌套类对象

​	该实现思想利用**利用程序在结束时析构全局变量的特性，来选择释放时机。**程序结束时，会自动调用静态变量_deleter的析构函数。

```C++
std::mutex g_mutex;
class Singleton{
public:
	static Singleton* getInstance(){
		if (!Singleton::_instance){
			g_mutex.lock();
			if (!Singleton::_instance){
				Singleton::_instance = new Singleton();
			}
			g_mutex.unlock();
		}
		return Singleton::_instance;
	}
private:
	static Singleton* _instance;
    
    ~Singleton(){
		std::cout << "Singleton::~Singleton" << std::endl;
	}
    
	class Deleter{
	public:
		~Deleter(){
			if (Singleton::_instance)
				delete Singleton::_instance;
			std::cout << "delete Singleton " << std::endl;
		}
	private:
	};
	static Deleter _deleter;
};
Singleton* Singleton::_instance = NULL;
Singleton::Deleter Singleton::_deleter;				//初始化_deleter，必须
```



#### 智能指针

```C++
std::mutex g_mutex;
class Singleton{
public:
    //析构函数需要声明为public，否则智能指针无访问权限
	~Singleton(){
		std::cout << "Singleton::~Singleton" << std::endl;
	}
	static Singleton* getInstance(){
		if (!instance){
			g_mutex.lock();
			if (!instance){
				instance = std::shared_ptr<Singleton>(new Singleton);
			}
			g_mutex.unlock();
		}
		return instance.get();
	}
private:
	static std::shared_ptr<Singleton> instance;
};
std::shared_ptr<Singleton> Singleton::instance = NULL;
```



### 饿汉模式

​	饿汉版是在单例实例在程序运行时被立即执行初始化。

> **函数内static对象称为local static 对象，而其它static对象称为non-local static对象。**
>
> 这两者在构造上存在一些细微的差别：
>
> - 对于local static对象，在其所属函数被调用之前，并不存在，即只有在第一次调用对应函数时，local static对象才被构造出来。
> - 对于non-local static对象，在main()函数开始前就已经被构造出来，并在main()函数结束后被析构。
>

​	基于以上的理论分析，```instance```属于no-local static对象在main函数之前被初始化，所以不存在线程安全的问题，但是在不同的编译中初始化顺序未定义的，即```static Singleton instance```和```static Singleton& getInstance()```两者的初始化顺序不确定，如果在```instance```完成初始化之前调用```getInstance()```会返回一个未定义的实例。

```C++
class Singleton
{
private:
	static Singleton instance;
public:
	static Singleton& getInstance() {
		return instance;
	}
};
Singleton Singleton::instance;
```



### Meyers' Singleton

​	**C++11规定：当一个线程正在初始化一个变量的时候，其他线程必须得等到该初始化完成以后才能访问它。**因此在C++11的标准下提出了Meyers' Singleton模型，该方式再C++11之前是线程不安全的，在C++11之后是线程安全的。

```C++
class Singleton2
{
public:
	static Singleton2& getInstance(){
		static Singleton2 instance;
		return instance;
	}
};
```

