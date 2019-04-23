---
layout: post
title: Python常用IDE和命令
date: 2019-4-16 15:00
category: Python
tags: [Python]
description: 
---





​	Anaconda集成了各类Python工具的集成平台，方便于对不同版本的Python进行管理。

```shell
conda --version				#查看版本号
```



### IPython

​	IPython是一个交互式shell，适合进行交互式数据可视化和GUI相关的应用。

#### %魔术命令

```shell
%magic					#显示所有的魔术命令
%hist					#查看IPython的输入命令史
%pdb					#异常发生后自动进入调试器
%reset					#删除当前命名空间中的全部变量或名称
%who					#显示IPython中当前命名空间中已定义的变量
%time <statement>		#statement代码的执行时间
%timeit <statement>		#多次执行statement代码，计算综合平均执行时间
```



