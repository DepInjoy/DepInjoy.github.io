---
layout: post
title: Java入门
date: 2018-05-12 20:00
category: Java
tags: [Java]
description: Java入门，以打印Hello World为例，介绍对代码编译执行、类加载路径以及一些基础语法等等。
---

### 入门之Hello World

```java
/*
	File Name： HelloWorld.java
	Function：  Print Hello World in windows
*/
public class HelloWorld{
    public static void main(String args[]){
	System.out.println("Hello World");
    }
}
```

#### 编译执行

- 1  编译源文件：执行javac HelloWorld.java形成HelloWorld.class二进制字节码文件

- 2  解释java程序：java HelloWorld

- 3  终端打印出Hello World，则程序执行成功

#### 类的加载路径

Java一般采用默认加载机制，即当前所在路径(.)，同时也可以通过```SET CLASSPATH=XXXX```设置类的加载路径。比如我们将上述程序编译生成的HelloWorld.class复制到当前路径下的tmp路径下，此时执行java HelloWorld会报错，执行``````SET CLASSPATH=../```设置上一级路径为类的加载路径，则程序执行成功。

#### 基础语法

- 1 Java中的基本单位是类，有两种声明方式：

  - 1  public class 类名称{},文件名称和类名称必须保持一致，类名称的首字母必须大写
  - 2  class 类名称{}，文件名称和类名称可以不一致，编译生成的二进制字节码名称和类名称一致，一个文件中定义多个类，编译会生成多个和类名称一致的.class，一般不在同一个文件中声明多个类。

- 2  Java程序的入口

  ```java
  public static void main(String args[]) 
  ```

- 3  Java的标识符由字符、数值、_、$组成，不能由数字开头，不能使用Java中的保留关键字。
- 4 消息打印

```java
System.out.println()	//打印日志并自动换行
System.out.print()		//打印日志无换行
```

#### 注释

注释不会被编译器编译，帮助阅读程序的人理解。

```java
1.	单行注释 //注释内容
2.	多行注释/*注释内容*/
3.	文档注释/**文档注释标记*/

```



### 数据类型

```java
public class ParameterType{
	public static void main(String arg[]){
		int intMax = Integer.MAX_VALUE;
		int intMin = Integer.MIN_VALUE;
		System.out.println(intMax);				//2147483647
		System.out.println(intMin);				//-2147483648
		System.out.println(intMax + 1);			//-2147483648
		System.out.println(intMax + 2);			//-214748367
		System.out.println(intMin - 1);			//2147483647			
		System.out.println(intMin - 2);			//2147483646
		
		long tmp = intMax + 1;
		System.out.println(tmp);			//-2147483648
		tmp = (long)intMax + 1;
		System.out.println(tmp);			//2147483648
		
		String str = "Hello neice to meet you !";
		int a = 10;
		int b = 8;
		String out = str + a + b;
		System.out.println(out);		//Hello neice to meet you !108
		String out1 = str + (a + b);
		System.out.println(out1);		//Hello neice to meet you !18
	}
}
```

### 递归示例

```java
public class Recursive{
	public static void main(String arg[]){
		System.out.println(sum(100));
	}
	public static int sum(int num){
		if(num == 1)
			return 1;
		return num + sum(num - 1);
	}
}
```

