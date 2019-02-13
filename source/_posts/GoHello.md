

## 环境搭建

[go安装白下载地址]( https://golang.org/dl/)

### windows环境搭建

#### 配置环境变量

安装完go会默认添加`GOPATH`值为```%USERPROFILE%\go```并在`Path`追加`;%GOPATH%\bin`。我们需要确定添加的路径和实际的安装路径是否一致。

在cmd中输入```echo %USERPROFILE%```查看输出是否和您的go安装路径一致。如果不一致，则将```%USERPROFILE%```替换为性对应的go安装路径。

### 辅助开发工具VSCode

[VSCode/VSC](https://code.visualstudio.com/download)是微软开发的一款免费支持开源跨平台(支持 Win、Mac 以及 Linux)的现代化轻量级代码编辑器，它支持几乎所有主流的开发语言的语法高亮、智能代码补全、自定义快捷键、括号匹配和颜色区分、代码片段、代码对比 Diff、GIT命令 等特性，支持插件扩展，并针对网页开发和云端应用开发做了优化。

