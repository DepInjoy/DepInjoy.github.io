---
layout: post
title: Spring初识
date: 2019-03-26 20:00
category: Spring
tags: [Spring，Java]
description: 
---

​	本文是针对跟着极客时间《玩转Spring全家桶》学习的一个记录，既是对学习过程的整理，也希望在此过程中有新的思考和感悟。

### 初建工程

​	目前大多数大多数使用的Idea、Eclipse集成开发工具，我选择使用Idea，在本文基于此进行一些介绍，首先推荐[博客](http://tengj.top/2017/02/26/springboot1/)很详细描述了如何从零构建一个项目。和其他的编程语言类似，以打印Hello World为例构造第一个Spring工程。

1. 通过Web下载[项目安装](http://start.spring.io)，填写Group、Artifact以及dependencies，之后点击生成工程。

2. 将下载的项目工程包解压

3. 打开IDea，选择文件->Import Project，在对话框中找到工程目录打开。

   ​	如果找不到Import Project选项可以[参考博客](https://blog.csdn.net/zengxiaosen/article/details/52807540)手动添加。

4. 编写实现代码

   ```Java
   package com.airplan.manager;
   
   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;
   import org.springframework.web.bind.annotation.RequestMapping;
   import org.springframework.web.bind.annotation.RestController;
   
   @SpringBootApplication
   @RestController
   public class AirplanManagerApplication {
   
   	public static void main(String[] args) {
   		System.out.println("Hello Spring !");
   		SpringApplication.run(AirplanManagerApplication.class, args);
   	}
   	@RequestMapping("/")
   	public String showMainPage(){
   		return "Hello Spring";
   	}
   }
   ```

5. 工程打开后，右键，选择运行该项目即可。

6. 在网页输入http://localhost:8080/hello即可看到Hello Spring字样。

#### 工程打包

- mvn clean compile：编译项目主代码
- mvn clean package：工程打包，生成jar文件位于target目录下，执行java -jar XXXXXX.jar就可以运行程序。
- mvn clean install：将项目生成的jar安装早Maven本地仓库
- mvn clean test：执行测试方法。

#### 工程结构

- pom.xml： Maven构建说明文件。
- AirplanManagerApplication.java：应用程序执行入口。
- AirplanManagerApplicationTests.java：空的Junit测试类。

### JDBC数据源配置

#### 单数据源配置



#### 多数据源配置



### 问题附录

- 1 [intellij idea 如何配置jdk](https://jingyan.baidu.com/article/7082dc1c3ffd41e40a89bddf.html)
  JDK的配置在项目结构配置界面，打开项目结构配置页面有三种方式：
  - File->Project Structure
  - 快捷键ctrl+alt+shift+s
  - 点击右上角的项目目录结构配置按钮

- 2 [Error:(3, 32) java: 程序包org.springframework.boot不存在](https://www.cnblogs.com/mfmdaoyou/p/6817203.html)
  是由于pom文件的依赖相关的jar包没有导进来

- 3 [命令行执行mvn，报Error: JAVA_HOME not found in your environment.](https://blog.csdn.net/anjing0917/article/details/71056114)

  出现该问题是由于没有将IDEA中的maven插件加入系统的环境变量。

- 4 IDEA的终端不识别mvn、java等命令

  出现该问题的原因是IEAD的cmd没有进行配置，修改File->setting->Terminal 将shell path 修改为系统C:\Windows\System32\cmd.exe。

- 5 The Tomcat connector configured to listen on port 8080 failed to start. The port may already be in use or the connector may be misconfigured.

  出现该问题的原因是，在我们的电脑端运行了多个绑定在8080端口的程序，导致新建的进程无法绑定端口，进而倒是程序死掉，可以通过杀死绑定在8080端口的其他的进程。

  ```shell
  netstat -ano | findstr "8080"		#查询绑定在8080端口的进程
  	# TCP    0.0.0.0:8080           0.0.0.0:0              LISTENING       8952
    	# TCP    [::]:8080              [::]:0                 LISTENING       8952
  taskkill /pid 8952					#杀死pid为8952的进程
  taskkill /F /pid 8952				#强行杀死pid为8952的进程
  ```

- 