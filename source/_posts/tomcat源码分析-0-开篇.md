---
title: tomcat源码分析-0-开篇
date: 2017-12-22 16:01:14
tags:
categories: tomcat
---

# 什么是 tomcat

[tomcat 中文 wiki](https://zh.wikipedia.org/wiki/Apache_Tomcat)。总的来说，tomcat 是一个支持 **servlet 标准** 的 **web 容器**。

# 什么是容器

容器又名 container，为其内需要管理的对象的正常运行提供各种支持，包括但不限于生命周期、运行时机、资源分配、安全管理等。

当 web 服务器拿到一个请求时，不是直接交给 servlet 处理，而是先交给 web 容器。web 容器来决定如何处理该请求，相当于 web 请求的大管家。

tomcat 提供以下支持：

* 通信支持：建立 socket、监听某个端口、创建流等；
* 生命周期管理：控制 servlet 生死，负责加载类、实例化和初始化 servlet、调用 servlet 方法及使 servlet 实例能够被垃圾回收；
* 线程支持：自动为接收的每个 servlet 请求创建一个新的 java 线程；
* JSP 支持：对 JSP 请求动态生成 servlet 来处理。

# 什么是 servlet 标准

servlet 是指实现 Servlet 接口的类。[servlet 中文 wiki](https://zh.wikipedia.org/wiki/Java_Servlet)。所谓的 servlet 标准就是所有 servlet 程序都必须实现该接口或者继承实现了该接口的类。

# tomcat 和 nginx、apache 的区别

nginx 和 apache 是 HTTP 服务器，只支持静态网页，而 tomcat 是支持 servlet 标准的服务器，支持动态内容生成。

# 结束语

综上简单介绍了下 tomcat，接下来分阶段剖析 tomcat 源码。

以下是文章链接：

* {% post_link tomcat源码分析-1-准备工作 %}
* {% post_link tomcat源码分析-2-整体架构 %}
* {% post_link tomcat源码分析-3-HTTP协议解析 %}
* {% post_link tomcat源码分析-4-版本比较 %}

# 参考链接

* <https://zh.wikipedia.org/wiki/Apache_Tomcat>
* <http://benlee.iteye.com/blog/375214>
