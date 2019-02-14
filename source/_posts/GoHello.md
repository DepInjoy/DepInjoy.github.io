

## 环境搭建

### windows环境搭建

1. 安装包下载[地址]( https://golang.org/dl/)

2. 双击安装，安装完默认会添加如下环境变量：

   - 用户变量，该便令用于完成代码编写后指向的工作路径。
     - ```GOPATH```其值为```%USERPROFILE%\go```
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




