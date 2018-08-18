##### 分支介绍
master: 部署的博客系统<br/>
source：本地可以编译运行的完整的代码<br/>

##### 本地环境搭建
安装[NodeJs](https://nodejs.org/en/),之后执行```npm install hexo-cli -g```安装hexo<br/>
下载source分支的代码，分别执行下面的命令
```
# 安装渲染插件
npm install hexo-renderer-jade@0.3.0 --save
npm install hexo-renderer-stylus --save

# 生成博客静态网页
hexo generate

# 发布到server端，通过浏览器访问http://localhost:4000
hexo server
```

