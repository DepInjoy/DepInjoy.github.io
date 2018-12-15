---
layout: post
title: XML文件解析
date: 2017-03-16 20:00
category: Python
tags: [Python]
description: XML文件解析
---





XML学习网址:

[W3C](http://www.w3school.com.cn/xml/index.asp)

[tizag.com](http://www.tizag.com/xmlTutorial/index.php)

### XML简介

- 一种可扩展标记语言(*EX*tensible *M*arkup *L*anguage）
- 设计用来结构化、传输和存储数据。在大多数 web 应用程序中，XML 用于传输数据，而 HTML 用于格式化显示数据
- XML 是独立于软件和硬件的信息传输工具，支持处理纯文本的软件都可以处理 XML
- XML 没有预定义的标签，XML 允许创作者定义自己的标签和自己的文档结构。
- XML 元素是可扩展的，形成了一种树结构，从“根部”开始，然后扩展到“枝叶”。
- 几乎所有的主流浏览器均支持 XML 和 XSLT

#### XML应用

##### 1、 XML把数据从HTML中分离

##### 2、简化数据的共享、传输和变更

##### 3、XML独立于硬件、软件和应用程序，使得不同应用程序都可以访问相关数据 

##### 4、XML 用于创建新的 Internet 语言，如XHTML、WSDL、RSS、RDF和SMIL等等

#### XML语法

##### 实体引用

在 XML 中，一些字符拥有特殊的意义。如果你把字符 "<" 放在 XML 元素中，会发生错误，这是因为解析器会把它当作新元素的开始。在 XML 中，有 5 个预定义的实体引用：

| ```&lt;```   | <    | 小于   |
| ------------ | ---- | ------ |
| ``&gt;``     | >    | 大于   |
| ```&amp;```  | &    | 和号   |
| ```&apos;``` | '    | 单引号 |
| ```&quot;``` | "    | 引号   |

##### 验证 XML 文档

##### [XML DTD](http://www.w3school.com.cn/dtd/index.asp)

##### [XML Schema](http://www.w3school.com.cn/schema/index.asp)

### Python解析XML文档

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<achivements>
    <achivement id="1">
        <Math>90</Math>
        <English>80</English>
        <Chinese>68</Chinese>
    </achivement>
    <achivement id="2">
        <Math>83</Math>
        <English>75</English>
        <Chinese>62</Chinese>
    </achivement>
</achivements>
```

```Python
import xml.etree.ElementTree

tree = xml.etree.ElementTree.parse("demo.xml")
root = tree.getroot()
achievements = []
for achievement in root.findall("./achivement"):
    data={}
    data["id"] = achievement.attrib["id"]				#获取属性
    data["Math"] = achievement.find("Math").text		#
    data["English"] = achievement.find("English").text
    data["Chinese"] = achievement.find("Chinese").text
    achievements.append(data)
print(achievements)
'''
[{'id': '1', 'Math': '90', 'English': '80', 'Chinese': '68'}, {'id': '2', 'Math': '83', 'English': '75', 'Chinese': '62'}]
'''
```



