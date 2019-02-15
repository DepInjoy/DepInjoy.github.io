---
layout: post
title: 创建第一个Spring Boot工程
date: 2018-9-25 20:11
category: Spring Boot
tags: [Spring Boot]
description: 
---

### 构造第一个Spring工程

​	目前大多数大多数使用的Idea、Eclipse集成开发工具，我选择使用Idea，在本文基于此进行一些介绍。和其他的编程语言类似，以打印Hello World为例构造第一个Spring工程。

1. 通过Web下载[项目安装](http://start.spring.io)，填写Group、Artifact以及dependencies，之后点击生成工程。

2. 将下载的项目工程包解压

3. 打开IDea，选择文件->Import Project，在对话框中找到工程目录打开。

   ​	如果找不到Import Project选项可以[参考博客]()手动添加。

4. 工程打开后，右键，选择运行该项目即可。

# 初始化Spring Boot项目以及项目结构

[博客](http://tengj.top/2017/02/26/springboot1/)很详细描述了如何从零构建一个项目。

# 问题附录

- 1 [intellij idea 如何配置jdk](https://jingyan.baidu.com/article/7082dc1c3ffd41e40a89bddf.html)
JDK的配置在项目结构配置界面，打开项目结构配置页面有三种方式：
	- File->Project Structure
	- 快捷键ctrl+alt+shift+s
	- 点击右上角的项目目录结构配置按钮
- 2 [Error:(3, 32) java: 程序包org.springframework.boot不存在](https://www.cnblogs.com/mfmdaoyou/p/6817203.html)
是由于pom文件的依赖相关的jar包没有导进来