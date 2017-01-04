---
title: 如何让 Cocoapods 不同版本共存
date: 2017-01-04 10:24:15
tags:
categories: 工具
---

公司目前用的 cocoapods 版本是0.35，而平时自己研究别人源码用的都是1.0以上的版本。如果同时安装两个版本，再使用 `pod` 命令时，总是产生冲突，容易出错。此时两个版本的 cocoapods 会安装在同一个环境下，所以考虑如何让两个版本的 ，甚至多个版本的 cocoapods 安装在不同的环境下，那么版本共存和切换就有了可能性。


# 渔

首先我们从 cocoapods 是如何安装的入手分析。

```
sudo gem install cocoapods
```

这段代码是最基本的安装命令，执行后会安装最新的 cocoapods 版本。注意到，用的是 gem 命令，gem 又是个啥呢？

## gem

**gem 是封装起来的 Ruby 应用程序或代码库，终端使用的 gem 命令，是指通过 RubyGems 管理 Gem 包。也就是说 cocoapods 是一个 Ruby 代码包，用 gem 来安装。**

如果有个管理工具，可以安装和管理多个 Ruby 环境，在每个 Ruby 环境中，用 gem 安装 cocoapods，不就实现了不同版本的 cocoapods 共存了吗？ruby 刚好有个版本管理工具，可以做到这一点。

## RVM

**RVM 用于帮助你安装 Ruby 环境，帮你管理多个 Ruby 环境，帮你管理你开发的每个 Ruby 应用使用机器上哪个 Ruby 环境。**

总结下来就是，使用 RVM 安装不同版本的 Ruby 环境，在每个环境中安装 cocoapods，此时每个 Ruby 环境中的 cocoapods 可以不同。

渔完了，接下来是鱼。


# 鱼

需要 cocoapods 版本切换的你，肯定在机子上已经安装好了某个版本的 cocoapods，为了避免引起冲突，先把机器上的 cocoapods 卸载干净。

## 卸载当前机器上的 cocoapods

1. 终端输入 `gem list` ，查看当前 gem 下安装的所有包
2. 输入命令 `sudo gem uninstall XXX` ，XXX 是你要卸载的包名
3. 卸载步骤1中所有 cocoapods 开头的包
4. 反复执行前面几步，直到所有的 cocoapods 开头的包都已经被卸载了

## 安装 rvm 和多个版本的 Ruby

1. 安装 rvm，使用命令 `curl -L get.rvm.io | bash -s stable && source ~/.rvm/scripts/rvm`
2. 到 [https://rvm.io/binaries]() 下载相应版本的ruby包
3. 执行 `rvm mount ~/Downloads/ruby-2.2.3.tar.bz2` ，其中 ruby-2.2.3.tar.bz2 是你下载好的 ruby 包，按自己的需求可以多安装几个版本
4. 执行 `rvm list` 可查看当前安装的 ruby 版本列表，执行 `rvm use <Version>` 可切换到不同版本的 ruby
5. 在某个 ruby 版本下，安装指定版本的 cocoapods，参见下面内容

## 如何正确的安装 cocoapods

如果你是 mac 的10.11及以上系统，需要使用命令

```
sudo gem install cocoapods -v <Version> -n /usr/local/bin
```

否则的话使用命令

```
sudo gem install cocoapods -v <Version>
```

## 我安装的版本对应表

由于 ruby 版本不同，可安装的 cocoapods 版本也就不同，下面是我已经验证可行的版本对应关系。

| ruby version | cocoapods version |
| :----------: | :---------------: |
| 2.1.0        | 0.35              |
| 2.2.0        | 0.39              |
| 2.2.3        | 1.0.1             |


# 参考链接
- [https://henter.me/post/ruby-rvm-gem-rake-bundle-rails.html]()
- [http://manongs.com/a/GQOrmm]()
