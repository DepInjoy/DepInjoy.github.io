---
layout: post
title: 模板
date: 2018-03-26 20:00
category: CPP
tags: [CPP]
description: 以实例的形式描述对C++模板的理解。
---



模板是泛型编程的基础，一个模板是创建类或函数的蓝图（公式）。

## 函数模板

模板首先看下面的一个实例，实现查找不同类型（int, double等）的列表中的最大数。使用模板可以避免根据数据参数的类型编写很多函数。
```C++
#include <iostream>
using namespace std;
template<class T>
T max(T a[], int size) {
	T max = a[0];
	for (int i = 0; i < size; i++) {
		if (max < a[i])
			max = a[i];
	}
	return max;
}

int main(void)
{
	int a[5] = {2, 45, 6, 78, 29};
	cout << max(a, 5) << endl;		//78

	double b[8] = {3.4, 6.7, 1.2, 8,9, 120, 34.89};
	cout << max(b, 8) << endl;		//120
	return 0;
}
```
### 不进行模板实例化
```C++
#include <iostream>
using namespace std;

template<class T>
T inc(T n){
	return n + 1;
}

int main(void)
{
	int n = 8;
	cout << inc<double>(n) / 2 << endl;	//4.5
	cout << inc(n) / 2 << endl;		    //4
	cout << inc(8) / 2 << endl;			//4
	return 0;
}
```
### 匹配函数模板时不自动进行类型匹配
```C++
#include <iostream>
using namespace std;
template<class T>
T max(T a, T b) {
	T max = (a > b) ? a : b;
	cout << "template max " << max << endl;
	return max;
}
int main()
{
	int a = 6, b = 8;
	double c = 9.8, d = 10.2;
	max(a, c);
	return 0;
}
```
上面的代码编译时会给出下面的编译错误信息：
```
error C2784: “T max(T,T)”: 未能从“double”为“T”推导 模板 参数
```
在上面的代码中添加如下的模板函数就可以解决：
```C++
template<class T, class T1>
T max(T a, T1 b) {
	T max = (a > b) ? a : b;
	cout << "template max max " << max << endl;
	return max;
}
```
### 函数模板和函数的调用次序
如果有多个函数和函数模板名相同情况下，编译器进行如下函数调用语句：
- 1 先找参数完全匹配的普通函数(非模板实例化等到的)，参看实例的max(a, b)调用。
- 2 再找参数完全匹配的模板函数，参看下面示例的max(a, c);max(c, d);调用
- 3 再找实参经过自动类型转化能够匹配的普通函数(非模板实例化等到的)
- 4 如果上面都找不到，则报错。参看标题匹配函数模板时不自动进行类型匹配的示例。

```C++
#include <iostream>
using namespace std;
template<class T>
T max(T a, T b) {
	T max = (a > b) ? a : b;
	cout << "template max " << max << endl;
	return max;
}

template<class T, class T1>
T max(T a, T1 b) {
	T max = (a > b) ? a : b;
	cout << "template max max " << max << endl;
	return max;
}

int max(int a, int b) {
	int max = (a > b) ? a : b;
	cout << "cal max of int " << max << endl;
	return max;
}

int main()
{
	int a = 6, b = 8;
	double c = 9.8, d = 10.2;
	max(a, b);	//cal max of int 8
	max(a, c);	//template max max 9
	max(c, d);	//template max 10.2
	return 0;
}
```

## 类模板

编译器由类模板生成类的过程叫做类的实例化，由类模板实例化得到的类叫做模板类。

```C++
#include <iostream>
#include <string>
using namespace std;
template<class T1, class T2>
class Pair {
public:
	T1 key;
	T2 value;
	Pair(T1 k, T2 v) : key(k), value(v) {};
	//模板类中定义函数
	bool operator < (const Pair<T1, T2>& p) const;
	//在模板类中定义模板函数
	template<class T>
	void print(T data) { cout << data << endl; }
};

//实现Pair类中的operator<函数
template<class T1, class T2>
bool Pair<T1, T2>::operator<(const Pair<T1, T2>& p) const
{
	return value < p.value;
}

int main(int argc, char* argv[])
{
	//实例化模板类
    Pair<string, int> student0("Lucy", 18), student1("Lily", 19);
	bool res = student0 < student1;
	cout << "------------------- " << res << endl;				//1
	student0.print("Hello, template function in class");
	return 0;
}
```

- 同一个类模板的两个模板类是不兼容的。

  ```C++
  Pair<string, int>* tmp0;
  Pair<string, double> tmp1;
  tmp0 = &tmp1;				//出错
  ```

- 类模板的类型参数列表中可以出现非类型参数

  ```C++
  #include <iostream>
  #include <string>
  using namespace std;
  template<class T, int size>
  class TArray {
  	T array[size] = {0};
  public:
  	void print(void) {
  		for (int i = 0; i < size; i++)
  			cout << array[i] << " ";
  		cout << endl;
  	}
  };
  int main(int argc, char* argv[])
  {
  	TArray<int, 10> tmp;
  	tmp.print();
  	return 0;
  }
  ```

  ### 模板类与继承

  #### 类模板从类模板继承

  ```C++
  #include <iostream>
  #include <string>
  using namespace std;
  
  template<class T1, class T2>
  class A {
  public:
  	T1 v1;
  	T2 v2;
  	A() {}
  	A(T1 t1, T2 t2) :v1(t1), v2(t2) {
  		cout << "A:  v1 = " << v1 << ", v2 = " << v2 << endl;
  	};
  };
  
  template<class T1, class T2>
  class B :public A<T2, T1> {
  	T1 v3;
  	T2 v4;
  public:
  	B(T1 t3, T2 t4) :v3(t3), v4(t4), A(t3, t4) {
  		cout << "B:  v3 = " << v3 << ", v4 = " << v4 << endl;
  	}
  };
  
  template<class T>
  class C :public B<T, T> {
  	T v5;
  public:
  	C(T t5) :v5(t5), B(t5, t5){
  		cout << "C:  v5 = " << v5 << endl;
  	}
  };
  int main()
  {
      /*
      	A:  v1 = 6 v2 = 6
      	B:  v3 = 6.18 v4 = 6
      */
  	B<double, int> tmp1(6.18, 6); //B<double, int> A<int, double>
  	/*
      	A:  v1 = 6.18 v2 = 6.18
      	B:  v3 = 6.18 v4 = 6.18
      	C:  v5 = 6.18
      */
  	C<double> tmp2(6.18);//C<double> B<double, double> A<double, double>
  	return 0;
  }
  ```

  #### 类模板从模板类继承

  ```C++
  #include <iostream>
  #include <string>
  using namespace std;
  
  template<class T1, class T2>
  class A {
  public:
  	T1 v1;
  	T2 v2;
  	A() {}
  	A(T1 t1, T2 t2) :v1(t1), v2(t2) {
  		cout << "A:  v1 = " << v1 << ", v2 = " << v2 << endl;
  	};
  };
  
  template<class T1, class T2>
  class B :public A<double, int> {
  	T1 v3;
  	T2 v4;
  public:
  	B(T1 t3, T2 t4) :v3(t3), v4(t4), A(t3, t4) {
  		cout << "B:  v3 = " << v3 << ", v4 = " << v4 << endl;
  	}
  };
  
  int main()
  {
      /*
      	A:  v1 = 6.18 v2 = 6
      	B:  v3 = 6.18 v4 = 6
      */
  	B<double, int> tmp1(6.18, 6);
  	return 0;
  }
  ```

  #### 普通类从模板类继承

  ```C++
  #include <iostream>
  #include <string>
  using namespace std;
  
  template<class T1, class T2>
  class A {
  public:
  	T1 v1;
  	T2 v2;
  	A() {}
  	A(T1 t1, T2 t2) :v1(t1), v2(t2) {
  		cout << "A:  v1 = " << v1 << ", v2 = " << v2 << endl;
  	};
  };
  
  class B :public A<double, int> {
  	double v3;
  public:
  	B(double t3) :v3(t3), A(t3, t3) {
  		cout << "B:  v3 = " << v3 << endl;
  	}
  };
  
  int main()
  {
  	/*
  		A:  v1 = 6.18 v2 = 6
  		B:  v3 = 6.18
  	*/
  	B tmp1(6.18);
  	return 0;
  }
  ```

  #### 模板类从普通类继承

  ```C++
  #include <iostream>
  #include <string>
  using namespace std;
  template<class T1, class T2>
  class A: public B {
  public:
  	T1 v1;
  	T2 v2;
  	A() {}
  	A(T1 t1, T2 t2) :v1(t1), v2(t2),B(t1) {
  		cout << "A:  v1 = " << v1 << ", v2 = " << v2 << endl;
  	};
  };
  
  class B  {
  	double v3;
  public:
  	B(double t3) :v3(t3){
  		cout << "B:  v3 = " << v3 << endl;
      }
  };
  int main()
  {
  	/*
  		B:  v3 = 6.18
  		A:  v1 = 6.18 v2 = 8
  	*/
  	A<double, int> tmp(6.18, 8);
  	return 0;
  }
  ```

  ### 模板类与友元

  #### 函数模板作为类模板的友元

  ```C++
  #include <iostream>
  #include <string>
  using namespace std;
  template<class T1, class T2>
  class Pair {
  public:
  	T1 key;
  	T2 value;
  	Pair(T1 k, T2 v) : key(k), value(v) {};
  	bool operator < (const Pair<T1, T2>& p) const;
  	template<class T3, class T4>
  	friend ostream& operator <<(ostream& o, const Pair<T3, T4>& p);
  };
  //实现Pair类中的operator<函数
  template<class T1, class T2>
  bool Pair<T1, T2>::operator<(const Pair<T1, T2>& p) const
  {
  	return value < p.value;
  }
  //任意Pair模板类的友元
  template<class T1, class T2>
  ostream& operator <<(ostream& o, const Pair<T1, T2>&p)
  {
  	o << "key = " << p.key << ",value = " << p.value;
  	return o;
  }
  int main(int argc, char* argv[])
  {
  	Pair<string, int> student("Lucy", 18);
  	Pair<double, int> tmp(6.18, 8);
  	cout << student << endl;	//key = Lucy,value = 18
  	cout << tmp << endl;		//key = 6.18,value = 8
  	return 0;
  }
  ```

  #### 函数模板作为类的友元

  ```C++
  #include <iostream>
  #include <string>
  using namespace std;
  class A {
  	double v;
  public:
  	A(double t) :v(t) {
  		cout << "A:  v = " << v << endl;
  	}
  	template <class T>
  	friend void print(const T& p);
  };
  template <class T>
  void print(const T& p)
  {
  	cout << "print value: " << p.v << endl;
  }
  int main(int argc, char* argv[])
  {
      /*
      	A:  v = 6.18 
      	print value: 6.18
      */
  	A tmp(6.18);
  	print(tmp);
  	//int b = 10;print(b); //错误
  	return 0;
  }
  ```

  ### 类模板和静态成员

  类模板中可以包含静态成员，从该类模板实例化得到的模板类都包含该静态成员。

  ```C++
  
  ```


