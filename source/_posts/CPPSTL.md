---
layout: post
title: 标准模板库STL编程
date: 2018-3-27 18:04
category: CPP
tags: [CPP]
description: 
---

​	标准模板库STL是用模板编程思想，将常用的数据库(如链表、数组和二叉树等)和算法(如排序、查找等)写成模板，实现无论数据结构中存放的数据对象是什么，都不必重新实现数据结构和算法。STL就是一些常用的数据结构和算法模板的集合，从而实现不必重新实现多达数标准的数据结构和算法，不能够得到很高的性能。

### 基本概念

#### 容器

​	容器是用于容纳各种数据类型(基本类型变量、对象)通用的数据结构，是类模板。对象插入容器中时，被插入的对象是复制品，有些算法需要对元素进行排序、查找和比较，而有的容器本身就已经进行排序，导致很多容器所属的类常常需要重载==和<元素符。容器可分为三类：

1. 顺序容器

   - vector

     头文件```<vector>```，vector可以看做动态扩容的数组，vector和数组的区别在于数组在声明时需要指定数组长度，而vector会根据运行情况动态分配内存，并且一般会多申请一些内存。vector操作时间复杂度：

     以索引的方式访问任意元素的时间复杂度为O(1)，在尾部增加或者删除元素的时间复杂度也是O(1)，而在元素中间插入或删除的时间复杂度为O(n)。由此可见**vector在尾部增加或删除元素具有更好的性能**。

   - deque

     头文件 ```<deque>```，deque是双向队列。**deque在两端增加或删除元素具有较好的性能。**

     - 支持随机访问，即支持[]以及at()，但是性能次于vector；
     - 支持在元素内进行插入和删除操作，但性能不及list。

   - list

     头文件``` <list> ```，list是双向链表。

     - 不支持随机存取。
     - 元素在内存不连续放任何位置增删有较好的性能。 

2. 关联容器

   1. 插入任何元素，都按相应的排序规则(从小到大)来确定其位置
   2. 查找时具有很好的性能
   3. 通常以平衡二叉树方式实现， 插入和检索的时间都是 O(log(N))。

   - set/multiset

     集合，set和multiset的区别在于set不允许元素重复，而multiset则允许有元素重复。

   - map/multimap

     map和set的区别在于map存放的元素有且只有两个成员变量，map根据第一个成员变量大小进行从小到大的排序并可以根据第一个成员变量进行进行元素检索。而map和multimap的区别在于是否允许有相同的第一个成员变量。

3. 容器适配器

   - stack

     头文件``` <stack>```，先进后出，删除、查找和修改项只能是最近插入的项(栈顶元素)

   - queue

     头文件```<queue>```，先进先出，删除、查找和修改项只能在头部进行。

   - priority_queue

     优先级队列，最高优先级的元素总是第一个出列。

#### 迭代器

​	迭代器用于指向顺序容器和关联容器中的元素，用法类似于指针，包含const和非const两种，其中非cons支持修改其指向的元素。

##### 迭代器定义

​	我们可以在迭代器上使用++操作来访问容器中的下一个元素，当迭代器已经指向容器中的最后一个元素时，不可以再执行++操作，否则会出现和访问空指针一样的情况。

```C++
容器名::iterator 迭代器变量名；					//非const迭代器
容器名::const_iterator 迭代器变量名;				//const迭代器
*迭代器变量名									  //访问迭代器指向的元素
```

以vector为例进行操作展示：

```C++
#include <iostream>
#include <vector>
using namespace std;
int main(int argc, char* argv[])
{
	vector<int> v0;
	v0.push_back(6);			//尾部追加元素
	v0.push_back(8);
    
    //定义非const迭代器，*a = 16的修改是允许的
	vector<int>::iterator a = v0.begin();
    //定义const迭代器，*b = 18的修改是不被允许的，但是b = a的操作是允许的
	vector<int>::const_iterator b = v0.begin();
	cout << *a << endl;			//6
	a++;						//指向容器中下一个元素
	cout << *a << endl;			//8
	cout << *b << endl;			//6
    
    /*		反向迭代器		*/
    vector<int>::reverse_iterator r = v0.rbegin();
	cout << *r << endl;			//8
	return 0;
}
```



#### 算法

​	算法是操作容器中元素或普通数组的的函数模板。算法与它操作的数据类型无关，可广泛用于任何数据结构中，大多数算法在```<algorithm>```中定义。STL中提供了各种容器[常用的算法](https://en.cppreference.com/w/cpp/algorithm)。

以find算法为例进行展示：

```C++
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

int main(int argc, char* argv[])
{
    /*
    	find函数定义：
    	template< class InputIt, class T >
		InputIt find( InputIt first, InputIt last, const T& value );
		
		find在[first, last)范围内进行查找,用==判断是否符合。
		@first		  查找起点
		@last		  查找终点
		@返回值		查找成功则返回相应的迭代器，如果失败则返回last迭代器。
    */
	vector<int> v;
	v.push_back(6);v.push_back(8);v.push_back(10);
	vector<int>::iterator d = find(v.begin(), v.end(), 6);
	cout << *d << endl;					//6
	vector<int>::iterator e = find(v.begin(), v.end(), 12);
	if (e == v.end()){					//Yes
		cout << "Not found !" << endl;
	}
	return 0;
}
```

##### STL中大、小和相等概念

- “大”、”小“

  在STL中，默认这样的描述等价：**x比y小**等价于**x<y为真**等价于**y比x大**

- 相等，有下面两种情况：

  - **x和y相等**等价于**x==y**,如顺序查找的find。

  - **x和y相等**等价于**x小于y和y小于x同时为假**，如binary_search。

    下面给出一个例子展示两种情况下相等的应用示例：

    ```C++
    #include <iostream>
    #include <vector>
    #include <map>
    #include <algorithm>
    using namespace std;
    class A
    {
    public:
    	int val;
    	A(int t);
        //必须为常量成员函数，否则编译会出错
    	bool operator ==(const A& v) const;
        //必须为常量成员函数，否则编译会出错
    	bool operator < (const A& v) const;
    };
    
    A::A(int t){
    	val = t;
    }
    
    bool A::operator==(const A& v)const
    {
    	cout << "overloaded == " << endl;
    	return val == v.val;
    }
    
    bool A::operator<(const A& v) const
    {
    	cout << "overloaded < " << endl;
    	return false;
    }
    
    int main(int argc, char* argv[])
    {
    	vector<A> v;
    	v.push_back(A(6)); v.push_back(A(8)); v.push_back(A(10));
    	/*
    		overloaded == 
    		--- 6
    	*/
        vector<A>::iterator d = find(v.begin(), v.end(), A(6));
    	cout << "--- " << d->val << endl;
    	/*
    		overloaded ==
    		overloaded ==
    		overloaded ==
    		Data not found !
    	*/
        d = find(v.begin(), v.end(), A(16));
    	if (d != v.end()) cout << "--- " << d->val << endl;
    	else cout << "Data not found !" << endl<< endl;
    
        /*
        	overloaded < 
        	overloaded < 
        	overloaded < 
        	binary_search result :1
        */
    	bool res = binary_search(v.begin(), v.end(), A(16));
    	cout << "binary_search result :" << res << endl;
    	return 0;
    }
    ```

#### 容器对比

|      容器      | 迭代器类别 | 操作时间复杂度 | 支持算法 |      |
| :------------: | :--------: | :------------: | :------: | :--: |
|     vector     |  随机访问  |                |          |      |
|    dequeue     |  随机访问  |                |          |      |
|      list      |    双向    |                |          |      |
|      set       |    双向    |                |          |      |
|    multiset    |    双向    |                |          |      |
|      map       |    双向    |                |          |      |
|    multimap    |    双向    |                |          |      |
|     stack      |   不支持   |                |          |      |
|     queue      |   不支持   |                |          |      |
| priority_queue |   不支持   |                |          |      |

其中：

双向迭代器支持以下运算：

p+=i;p-=i;p[i];p+i;p-i;p<p1;p<=p1;p>p1;p>=p1;p-p1(计算p到p1之间元素的个数)；

随机访问迭代器除了支持双向迭代器的运算之外，还支持支持以下运算：

++p;p++;--p;p--;*p;p!=p1;p==p1;

