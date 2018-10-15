---
layout: post
title: 创建第一个Spring Boot工程
date: 2018-9-25 20:11
category: Spring Boot
tags: [Spring Boot]
description: 
---

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