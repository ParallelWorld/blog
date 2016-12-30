---
title: 搭建博客（Github+Hexo+文章管理）
date: 2016-12-30 19:23:15
tags:
---


今天刚申请了个域名，就迫不及待的建了个人博客。下面是建立过程：


# 1. 新建自己的 Github Page
在自己的 Github 上新建一个 repository，取名` yourusername.github.io `。` README `和` gitignore `文件不用添加，所有选择默认即可。建好之后，是一个空的仓库，后面会用来保存生成的博客网页。


# 2. 安装 Hexo

由于 Hexo 这个博客框架更新很快，网上的很多相关博客其实都已经过时，所以还是参考[ Hexo 官网](https://hexo.io/zh-cn/)的教程为准。

主要命令如下：
```
npm install hexo-cli -g
hexo init blog
cd blog
npm install
```

除此之外，还需要修改` blog `文件夹下的` _config.yml `配置文件，关联之前创建的仓库。命令如下：
```
// 修改前
# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type:

// 修改后
# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: git
  repo: https://github.com/yourusername/yourusername.github.io.git
  branch: master
```

` type `、` repo `和` branch `之前一定要有两个空格，否则会报错。

为了能够识别` git `，还需要安装` hexo-deployer-git `包，命令如下：
```
npm install hexo-deployer-git --save
```

到此就可以本地写博客，并且可以发布到`https://github.com/yourusername/yourusername.github.io`网页上了。


# 3. 博客文章管理

除了上班的闲暇时间写写东西外，回家的时候可能也会写点，这时候就遇到了文章的同步问题。既然 Hexo 生成的博客网页可以存放在 github 上，那博客的文章为什么不能呢？所以新建一个仓库，取名` blog `，专门用来存放博客的源文件。

但是有个问题，就是有些文件是我们在生成博客网页的时候自动生成的。这些其实没有必要同步，而且每次都会重新生成，容易引起冲突。所以我们需要添加一个` .gitignore `文件，内容如下：

```
.DS_Store
Thumbs.db
db.json
*.log
node_modules/
public/
.deploy*/
```

当在新机器上写博客时，只需要把源文件 clone 下来，输入命令` npm install `即可。


# 参考链接
- http://www.jianshu.com/p/834d7cc0668d
- https://hexo.io/zh-cn
- https://hexo.io/zh-cn/docs/deployment.html
