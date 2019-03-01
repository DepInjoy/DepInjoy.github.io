---
layout: post
title: 标准模板库STL编程
date: 2018-3-27 18:04
category: CPP
tags: [CPP]
description: 
---

​	

​	标准模板库STL是用模板编程思想，将常用的数据库(如链表、数组和二叉树等)和算法(如排序、查找等)写成模板，实现无论数据结构中存放的数据对象是什么，都不必重新实现数据结构和算法。STL就是一些常用的数据结构和算法模板的集合，从而实现不必重新实现大多数标准的数据结构和算法，还能够得到很高性能。

## 基本概念

### 容器

​	容器是用于容纳各种数据类型(基本类型变量、对象)通用的数据结构，是类模板。对象插入容器中时，被插入的对象是复制品，有些算法需要对元素进行排序、查找和比较，而有的容器本身就已经进行排序，导致很多容器所属的类常常需要重载==和<元素符。容器可分为三类：



#### 顺序容器

##### vector

​	vector可以看做动态扩容的数组，vector和数组的区别在于数组在声明时需要指定数组长度，而vector会根据运行情况动态分配内存，并且一般会多申请一些内存。**vector在尾部增加或删除元素具有更好的性能**。

##### deque

​	deque是双向队列。**deque在两端增加或删除元素都具有较好的性能。**

- 支持随机访问，即支持[]以及at()，但是性能次于vector；
- 支持在元素内进行插入和删除操作，但性能不及list。

##### list

​	list是双向链表，不支持随机存取，且元素在内存不连续放任何位置增删有较好的性能。 



#### 关联容器

1. 插入任何元素，都按相应的排序规则(从小到大)来确定其位置
2. 查找时具有很好的性能，通常以平衡二叉树方式实现， 插入和检索的时间都是 O(log(N))。



##### set/multiset

​	集合，set和multiset的区别在于set不允许元素重复，而multiset则允许有元素重复。

```C++
//Compare决定了排序规则，如果采用默认的less模板，需要重载<.
template<
	class Key,
    class Compare = std::less<Key>,
    class Allocator = std::allocator<Key>
> class multiset;
```

使用示例：

```C++
#include <set>
#include <iostream>
using namespace std;

int main()
{
	set<int> tmp{1, 2, 3, 4, 3, 5};
	set<int>::iterator d;
	for (d = tmp.begin(); d != tmp.end(); d++){
		cout << *d << " ";
	}
	cout << endl;												//1 2 3 4 3 5
	
    //插入元素
	pair<set<int>::iterator, bool> status = tmp.insert(5);
	if (status.second)
		cout << "Insert successfully !" << endl;
	else
		cout << *status.first << " already existed !" << endl;	//5 already existed !
	
    /*	equal_range的返回值std::pair
    	first指向第一个比要查找的值小的元素，second返回指向第一个比要查找的值小大的元素
    */
	pair<set<int>::iterator, set<int>::iterator> range = tmp.equal_range(4);
	cout << *range.first << " " << *range.second << endl;					//4 5
	return 0;
}
```



##### map/multimap

​	map和set的区别在于map存放的元素有且只有first和second两个成员变量，map根据第一个成员变量大小进行从小到大的排序并可以根据第一个成员变量进行元素检索。而map和multimap的区别在于是否允许有相同的第一个成员变量。

```C++
//Compare决定排序规则，默认使用less模板
template < class Key,                                     // multimap::key_type
           class T,                                       // multimap::mapped_type
           class Compare = less<Key>,                     // multimap::key_compare
           class Alloc = allocator<pair<const Key,T> >    // multimap::allocator_type
> class multimap;
```



```C++
#include <map>
#include <iostream>
#include <string>
using namespace std;

int main()
{
	map<string, float> achieve {
				map<string, float>::value_type("Amy", 89),
				map<string, float>::value_type("Lily", 98), 
				map<string, float>::value_type("Bob", 95) 
	};
    achieve["Lucy"] = 86;						//不存在key值为Lucy的，则插入
	map<string, float>::iterator d;
	for (d = achieve.begin(); d != achieve.end(); d++)
		cout << d->first << " : " << d->second << endl;
	
    string key = "Bob";
	cout << achieve[key] << endl;				//95
	achieve[key] = 100;							//key值为Bob已经存在，则修改value值为100
	cout << achieve[key] << endl;				//100
    
    //向map中插入元素
	pair<map<string, float>::iterator, bool> status = achieve.insert(map<string, float>::value_type("Jack", 85));
	if (status.second)
		cout << "Insert successfully !" << endl;
	else
		cout << status.first->first << ":" << status.first->second << " has been inserted !" << endl;
	 
    //equal_range的返回first指向第一个比要查找小的元素，second返回指向第一个比要查找大的元素
	pair<map<string, float>::iterator, map<string, float>::iterator> res = achieve.equal_range("Bob");
	cout << "First	" << res.first->first << " : " << res.first->second << endl;	//First	Bob : 95
	cout << "Second "<< res.second->first << " : " << res.second->second << endl;	//Second Jack : 85
	return 0;
}
```



#### 容器适配器

##### stack

​	先进后出，删除、查找和修改项只能是最近插入的项(栈顶元素)。可以通过vector、list、dequeue来实现，缺省用dequeue实现。

| 操作 |      意义      |
| :--: | :------------: |
| push |    插入元素    |
| pop  |    弹出元素    |
| top  | 栈顶元素的引用 |



##### queue

​	先进先出，删除、查找和修改项只能在头部进行。和stack一样，它也可以使用list、dequeue来实现，缺省用dequeue实现。

| 操作 |       意义       |
| :--: | :--------------: |
| push |  在尾部插入元素  |
| back | 获取队尾元素引用 |
| pop  |   头部弹出元素   |
| top  | 获取队头元素引用 |



##### priority_queue

​	优先级队列，最高优先级的元素总是第一个出列。和queue类似，可以使用vector和的dequeue实现，缺省使用vector实现。它采用的堆栈技术，保证执行操作的元素总是优先级最大的元素，它采用的默认比较器是less模板。

```C++
//Container	声明容器类型
//Compare	制定排序规则
template<
    class T,
    class Container = std::vector<T>,
    class Compare = std::less<typename Container::value_type>
> class priority_queue;
```

| 操作 |               意义               | 操作时间复杂度 |
| :--: | :------------------------------: | :------------: |
| pop  |       删除优先级最高的元素       |    O(logN)     |
| push |             插入元素             |    O(logN)     |
| top  | 返回优先级最高的元素的**常引用** |      O(1)      |

操作示例：

```C++
#include <queue>
#include <iostream>
#include <functional>
using namespace std;

int main()
{
    //采用默认的vector容器以及less模板进行排序
	priority_queue<float> priQue;
	priQue.push(4.3);priQue.push(1.2);priQue.push(6.7);priQue.push(0.2);
	while (!priQue.empty()){
		cout << priQue.top() << endl;		//6.7 4.3 1.2 0.2
		priQue.pop();
	}
	
    //修改排序规则为greater
	priority_queue<float, vector<float>, greater<float> > priQue2;
	priQue2.push(4.3); priQue2.push(1.2); priQue2.push(6.7); priQue2.push(0.2);
	while (!priQue2.empty()){
		cout << priQue2.top() << endl;		//0.2 1.2 4.3 6.7
		priQue2.pop();
	}
	return 0;
}
```



### 迭代器

​	迭代器用于指向顺序容器和关联容器中的元素，用法类似于指针，包含const和非const两种，其中非cons支持修改其指向的元素。我们可以在迭代器上使用++操作来访问容器中的下一个元素，当迭代器已经指向容器中的最后一个元素时，不可以再执行++操作，否则会出现和访问空指针一样的情况。

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

### 算法

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

从支持的迭代器类、操作时间复杂度以及支持算法几个维度对常用的容器进行对比。

|      容器      | 迭代器类别 | 操作时间复杂度[插入、查询、删除] |                           支持算法                           |
| :------------: | :--------: | :------------------------------: | :----------------------------------------------------------: |
|     vector     |  随机访问  |         O(N)，O(1),O(N)          |                                                              |
|    dequeue     |  随机访问  |                                  | 所有适合于vector的都适用于dequeue，另外支持push_front和pop_front。 |
|      list      |    双向    |                                  |                                                              |
|  multiset/set  |    双向    |                                  | find(x>y&&y>x即为相等)，lower_bound，upper_bound，equal_range，count，insert。 |
|  multimap/map  |    双向    |                                  |                                                              |
|     stack      |   不支持   |                                  |                                                              |
|     queue      |   不支持   |                                  |                                                              |
| priority_queue |   不支持   |                                  |                                                              |

其中：

双向迭代器支持以下运算：

p+=i;p-=i;p[i];p+i;p-i;p<p1;p<=p1;p>p1;p>=p1;p-p1(计算p到p1之间元素的个数)；

随机访问迭代器除了支持双向迭代器的运算之外，还支持支持以下运算：

++p;p++;--p;p--;*p;p!=p1;p==p1;

## STL常用算法

​	在前面我们了解到C++提供的STL还提供了一系列的性能不错的算法实现，在使用这些算法之前，我们先要了解STL中的“大”、“小”和“相等”概念，默认：

```C++
x比y小等价于x<y等价于y比x大

x和y相等，则有如下两种情况：
x==y;比如顺序查找find函数。
(x<y)为假和(y>x)同时为假，如binary_search。
```

接下来自定义一个类A重载==和<运算符，调用find和binary_search算法展示两种不同不同情况的相等处理，实现代码如下：

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
    /*	
    	构造一个6，8,10组成的vector容器
    	由于要使用binary_search进行搜索，数据要按照大小进行排序
    */
	vector<A> v;
	v.push_back(A(6)); v.push_back(A(8)); v.push_back(A(10));
	return 0;
}
```

**测试用例一：**

```C++
    vector<A>::iterator d = find(v.begin(), v.end(), A(6));
	cout << "--- " << d->val << endl;
```

程序运行结果如下：

```C++
overloaded == 
--- 6
```

**测试用例二：**

```C++
    d = find(v.begin(), v.end(), A(16));
	if (d != v.end()) cout << "--- " << d->val << endl;
	else cout << "Data not found !" << endl<< endl;
```

运行结果：

```C++
overloaded ==
overloaded ==
overloaded ==
Data not found !
```

**测试用例三:**

```C++
	//查找任何数字都是相同的效果。
	bool res = binary_search(v.begin(), v.end(), A(16));
	cout << "binary_search result :" << res << endl;
```

运行结果：

```C++
overloaded < 
overloaded < 
overloaded < 
binary_search result :1
```

​	上面的用例可以看出find使用==从起始位置开始判断，为true则查找停止并返回，查找至末尾没有查找到则返回最后一个元素，表示查找失败。而binary_search则使用<进行判断，由于始终返回的都是false，导致算法以为第一个元素6就是查找的目标。

### 自定义排序规则

​	STL中自定义排序规则中默认使用greater/less函数模板，用户可以通过下述方式实现自定义排序规则：函数对象和重载<运算符。

#### 自定义函数对象比较规则

​	自定了比较器compare之后，下面的表述是等价的

```C++
x小于y等价于compare(x,y)为真等价于y大于x
```

​	关联容器中元素排序默认使用greater/less，用户可以通过函数对象实现自定义比较规则。

##### 定义函数对象

​	下面自定MyLess类实现将成员n的个位数较小为真，进行使用实例展示。

```C++
#include <iostream>
#include <algorithm>
#include <functional>
#include <list>
using namespace std;
class MyLess
{
public:
	bool operator()(int total, int b);
};
bool MyLess::operator()(int a, int b)
{
	//按照个位数大小进行排序
	return (a % 10) > (b % 10);
}
int main(int argc, char* argv[])
{
	list<int> l;
	l.push_back(41); l.push_back(22); l.push_back(37); l.push_back(16);
	list<int>::iterator tmp;
	return 0;
}
```

**测试用例一：**

```C++
	//按照MyLess函数对象规则进行排序，并将排序结果打印。
	l.sort(MyLess());
	for (tmp = l.begin(); tmp != l.end(); tmp++){
		cout << *tmp << " ";
	}
	cout << endl;					//37 16 22 41
```

**测试用例二：**

```C++

	l.sort(less<int>());			//从小到大排序
	for (tmp = l.begin(); tmp != l.end(); tmp++){
		cout << *tmp << " ";
	}
	cout << endl;					//35 21 16 2
```

##### 重载<运算符

​	sort函数默认使用less模板进行大小比较，而less模板是使用<进行大小判断的，下面两种实现分别重载<运算符以及用compareA替换默认的less来实现排序并达到了相同的效果。

```C++
#include <iostream>
#include <algorithm>
#include <functional>
#include <list>
using namespace std;
class A
{
public:
	int n;
	A(int a){ n = a; };
	friend bool operator< (const A& a, const A& b);
};

bool operator <(const A& a, const A& b)
{
	return (a.n % 10) > (b.n % 10);
}

int main(int argc, char* argv[])
{
	list<A> l;
	l.push_back(A(41)); l.push_back(A(22)); l.push_back(A(37)); l.push_back(A(16));
	list<A>::iterator tmp;

	l.sort();
	for (tmp = l.begin(); tmp != l.end(); tmp++){
		cout << tmp->n << " ";
	}
	cout << endl;					//37 16 22 41
	return 0;
}
```



```C++
#include <iostream>
#include <algorithm>
#include <functional>
#include <list>
using namespace std;
class A
{
public:
	int n;
	A(int a){ n = a; };
};

struct compareA{
	bool operator()(const A& a, const A& b)
	{
		//按照个位数大小进行排序
		return (a.n % 10) > (b.n % 10);
	}
};

int main(int argc, char* argv[])
{
	list<A> l;
	l.push_back(A(41)); l.push_back(A(22)); l.push_back(A(37)); l.push_back(A(16));
	list<A>::iterator tmp;

	l.sort(compareA());
	for (tmp = l.begin(); tmp != l.end(); tmp++){
		cout << tmp->n << " ";
	}
	cout << endl;					//37 16 22 41
	return 0;
}
```







ostream_iterator

#### 

