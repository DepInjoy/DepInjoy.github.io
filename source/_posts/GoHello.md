---
layout: post
title: Go语言入门
date: 2019-2-28 21：06
category:Go
tags: [Go]
description: 出于个人堆知识体系搭建考虑，今年下定决心学习服务器，大热的Go自是不能错过，为此购买了极客时间的《Go核心编程30讲》辅助学习，在此结合自己的学习实践整理笔记。本文会从基础的环境搭建、Go编程语言基础等几个方面记录所学所得。
---

​	时间如逝，很快我即将成为一个进入职场四年的白领，四年的时光，有很多的小确幸，朋友不多，却很幸运地在他们的帮助下，具备了在职场分析问题的能力，也偶尔可以相约小酌两杯，不幸的是，也看到了人性很复杂的一面，庆幸的是，当下的自己内心很宁静，有一点点满足，想珍惜那些重要的，慢慢地找自己的生活节奏，人生漫漫，慢慢来。机缘巧合下，很早进入无人机这个行业，说巧不巧，接触了嵌入式、上位机，编程语言从C到C++，再到Qt、Python，后来还零星学了些html、CSS、R。今年的我却很想学习一些服务器方面的知识，深入了解并发、分布式，有人说不知道方向的时候就行动吧，慢慢就知道自己想要的是什么。为此我购买了极客时间的《Go核心编程30讲》，此文做为我学习的一个记录，希望人生前进永不止步。

​	首先，先列出几个go学习的资源

- [Go中国官网](https://golang.google.cn/)
- [Go语言编程规范-英文](https://golang.google.cn/ref/spec)
- [Go 并发编程实战作者：郝林的Go命令教程](https://github.com/hyper0x/go_command_tutorial)



## 环境搭建

### windows环境搭建

1. 安装包下载[地址]( https://golang.org/dl/)

2. 双击安装，安装完默认会添加如下环境变量：

   - 用户变量，该变量用于完成代码编写后指向的工作路径。
     - ```GOPATH```默认值为```%USERPROFILE%\go```
     - ```Path```追加```;%GOPATH%\bin```
   - 系统环境变量，安装Go之后会自动将实际的安装路径添加至系统变量，我们假设采用默认的安装路径```C:\Go\```来进行说明。
     - ```GOROOT```它的值为```C:\GOo\```
     - ```Path```后追加```;C:\Go\bin```

3. 安装辅助开发工具VSCode[下载地址](https://code.visualstudio.com/download)，双击安装

   ​	VSCode/VSC是微软开发的一款免费支持开源跨平台(支持 Win、Mac 以及 Linux)的现代化轻量级代码编辑器，它支持几乎所有主流的开发语言的语法高亮、智能代码补全括号匹配和颜色区分、代码片段、代码对比 diff、git命令 等特性，支持插件扩展，并针对网页开发和云端应用开发做了优化。

4. 安装Go插件。打开VScode，输入```Ctrl+shit+x```，输入```Go```,在输出的结果中查找微软的Go插件并安装

5. 新建Go工程。文件->新建文件->Ctrl+S保存->选择保存路径(为方便后续描述假设为D:\helloGo)->选择文件后缀为.go，输入文件名

6. 编写代码

   ```go
   package main
   import "fmt"
   func main() {
       fmt.Println("hello, world")
   }
   ```

7. 将用户环境变量中的```GOPATH```修改为工程所在路径（D:\helloGo）。

8. 在VSCode下方的终端进入工程路径，执行```go build```，编译成功会生成```helloGo.exe```

9. 执行```helloGo.exe```，打印出```hello, world```至此环境搭建完成。



### 编程基础

​	本博客是我对极客时间《Go核心编程30讲》学习的一个记录，该专栏会基于Go面试的一些知识进行讲解，我也会对这些进行一个记录、验证和自我探索，好了下面就开始Go基础的学习。

#### 工作区和GOPATH

​	在上面的安装过程中，安装Go我们还需要配置3个环境变量：

- GOROOT：Go安装路径。
- GOBIN：Go可执行文件的存放路径。
- GOPATH：用户自定义的工作空间集。



###### 面试题1：GOPATH的意义是什么？

​	GOPATH对应于Go的工作空间，其值是一个或多个目录路径，每个路径都代表一个工作区间，每个工作区间内存放go的源码文件、归档文件(.a)和可执行文件。

​	事实上Go项目的整个生命周期内所有操作（编译、依赖管理、构建、测试、安装等）后市围绕GOPATH和工作区进行的，该面试题的背后包含如下的知识点：

- **Go语言源码的组织形式是怎样的？**
- **源码安装后的结果是怎样的？**
- **构建和安装Go程序的过程是怎样的？**
- 

#### Go编程语言规范

##### 源码文件如何接受输入

```go
/*
    go run .\Hello.go -name="Lucy"
        Hello, Lucy !
    go run .\Hello.go
        Hello, everyone !    
    go run .\Hello.go --HELP
        Usage of C:\Users\hp\AppData\Local\Temp\go-build817441770\b001\exe\Hello.exe:
            -name string
            Greeting object (default "everyone")
*/
package main
import(
    "flag" ;
    "fmt"
)

var name string
func init(){
    /*
    @param0 定义了一个有指定名字。参数p指向一个存储标签解析值的string变量。
	@param1 指定参数名 应用的时候 在命令行输入 -Args xxx
	@param2 如果没有指定Args的值，那么Args的内容默认是"defaultValue"
	@param3 用法说明字符串
    */
    flag.StringVar(&name, "name", "everyone", "Greeting object");
}

func main()  {
    flag.Parse();
    fmt.Printf("Hello, %s !\n", name);
}
```



#### Go命令

#### Go基础编程

#### Go并发编程






