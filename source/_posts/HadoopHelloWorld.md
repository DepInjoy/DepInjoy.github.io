---
layout: post
title: Hadoop入门
date: 2017-3-18 18:04
category: Hadoop
tags: [Hadoop]
description: 
---



### Hadoop环境搭建

​	Hadoop是运行在Linux上，虽然我们可以借助工具在windows上运行，对于初学建议在Linux上运行以避免踩不必要的坑，Linux系统的安装可以自行百度虚拟机的安装方法，其中可以参考[详细的Hadoop环境搭建](https://gitbook.cn/books/5954c9600326c7705af8a92a/index.html)博客。下面介绍我在Linux下V3.2.0环境搭建和测试步骤：

- 下载Hadoop安装包

   [Hadoop安装包下载地址](http://hadoop.apache.org/releases.html)，我选择了当前最新的3.2.0，并结合该版本给出环境搭建的步骤，大家可以根据自己的需要下载Hadoop安装包。

- 下载Java JDK

   由于Hadoop是基于Java的，我们还需要安装Java JDK，我们可以根据[查询Java JDK和Hadoop版本之间匹配关系](https://cwiki.apache.org/confluence/display/HADOOP/Hadoop+Java+Versions)，例如我下载了目前最新的3.2.0，根据介绍我需要Java8，之后参考[Unbuntu下搭建Java安装环境](https://jingyan.baidu.com/article/86fae346b696633c49121a30.html)，忽略eclipse部分，其中可以参考下面的命令，以记事本方式编辑/etc/profile

  ```shell
  sudo gedit /etc/profile				#以超级用户权限用记事本方式编辑/etc/profile文件
  source /etc/profile					#使得修改生效
  java -version						#查看java版本信息
  
  # 出现如下运行结果则表明环境配置完成
  # 	java version "1.8.0_201"
  # 	Java(TM) SE Runtime Environment (build 1.8.0_201-b09)
  # 	Java HotSpot(TM) 64-Bit Server VM (build 25.201-b09, mixed mode)
  ```

-  安装ssh和pdsh

```sudo apt-get install ssh pdsh
sudo apt-get install ssh pdsh
```

- 为Hadoop配置Java

  在解压后的Hadoop安装包中，打开etc/hadoop/hadoop-env.sh文件，设置export JAVA_HOME=/XXX/XXX

- 运行Hadoop自带的 wordcount在本地测试Mapreduce

  - 准备一个wc.txt文件

    ```
    hadoop mapreduce hive
    hbase spark storm
    sqoop hadoop hive
    spark hadoop
    ```

  - 运行Mapreduce Demo

    ```shell
    bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-3.2.0.jar wordcount /home/depjoy/develop/00_code/00_BigData/00_Hadoop/00_test/wc.txt output
    ```

    在打印信息中看到```INFO mapreduce.Job: Job job_local```表明是在本地运行。​	

#### 本地模式

​	本地模式只是适用于本地开发调试或者快速安装体验。默认情况下，Hadoop以非分布式模式，单Java进行方式运行。下面的示例来自Hadoop官方文档，查找并显示指定正则表示式匹配项，并将结果存放在output目录中。

```shell
mkdir input
cp etc/hadoop/*.xml input
bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-3.2.0.jar grep input output 'dfs[a-z.]+'
cat output/*
```

#### 伪分布式模式

​	

#### 完全分布模式

​	生产环境下采用的模式，而在实际的使用中一般都会做HA来实现高可用性。

#### HA安装

