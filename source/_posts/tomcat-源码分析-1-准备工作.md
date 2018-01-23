---
title: tomcat源码分析-1-准备工作
date: 2017-12-25 11:01:14
tags:
categories: tomcat
---

公司服务器上的 tomcat 使用的版本是`7.0.42`，所以这里调试使用的源码版本也是`7.0.42`。

[tomcat 源码地址](http://archive.apache.org/dist/tomcat/tomcat-7/v7.0.42/src/)。

一般使用 maven 来管理代码，所以我们要创建一个 maven 工程来承接 tomcat 源码。搭建步骤如下：

1. idea 创建 maven 工程。不需要使用任何模版。
1. 解压源码，将 java 文件夹的内容拷贝到 java 中，conf 和 webapps 文件夹拷贝到 resources 中。
1. 找到`Bootstrap`类，直接启动 main 函数，发现会报很多错。此时我们需要修改 pom 文件，解决 tomcat 的包依赖。
1. 修改 pom 文件，见下文。
1. 再运行，发现还是有错。观察发现，需要设置 vm 启动参数。

{% asset_img tomcat-1-vm1.png %}
{% asset_img tomcat-1-vm2.png %}

```xml
-Dcatalina.home=target/classes/
-Dcatalina.base=target/classes/
-Djava.endorsed.dirs=${catalina.base}endorsed
-Djava.io.tmpdir=${catalina.base}temp
-Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager
-Djava.util.logging.config.file=${catalina.base}conf/logging.properties
```

如果觉得这样下载源码再配置很麻烦，可以直接到[Tomcat7_0_42-maven](https://github.com/ParallelWorld/Tomcat7_0_42-maven)clone 项目源码，按照 README 操作即可。

我们启动`Bootstrap`的`main`函数，控制台显示如下信息即为成功：

```
Dec 27, 2017 5:25:53 PM org.apache.coyote.AbstractProtocol start
INFO: Starting ProtocolHandler ["http-bio-8080"]
Dec 27, 2017 5:25:53 PM org.apache.coyote.AbstractProtocol start
INFO: Starting ProtocolHandler ["ajp-bio-8009"]
Dec 27, 2017 5:25:53 PM org.apache.catalina.startup.Catalina start
INFO: Server startup in 2023 ms
```

浏览器输入`http://localhost:8080/`，可以看到那只经典的猫。
{% asset_img tomcat.png %}

# code

pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>pw.parallelworld</groupId>
    <artifactId>tomcat7</artifactId>
    <version>1.0-SNAPSHOT</version>

    <dependencies>
        <dependency>
            <groupId>org.apache.ant</groupId>
            <artifactId>ant</artifactId>
            <version>1.9.6</version>
        </dependency>
        <dependency>
            <groupId>javax.xml.rpc</groupId>
            <artifactId>javax.xml.rpc-api</artifactId>
            <version>1.1.1</version>
        </dependency>
        <dependency>
            <groupId>wsdl4j</groupId>
            <artifactId>wsdl4j</artifactId>
            <version>1.6.2</version>
        </dependency>
        <dependency>
            <groupId>org.eclipse.jdt.core.compiler</groupId>
            <artifactId>ecj</artifactId>
            <version>4.2.2</version>
        </dependency>
    </dependencies>

    <build>
        <finalName>Tomcat</finalName>
        <resources>
            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.xml</include>
                    <include>**/*.dtd</include>
                    <include>**/*.properties</include>
                    <include>**/*.xsd</include>
                </includes>
            </resource>
            <resource>
                <directory>src/main/resources</directory>
            </resource>
        </resources>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>2.5.1</version>

                <configuration>
                    <encoding>UTF-8</encoding>
                    <source>1.6</source>
                    <target>1.6</target>
                </configuration>
            </plugin>
        </plugins>
    </build>

</project>
```

# ref

* <http://kael-aiur.com/tomcat源码解读/使用maven搭建tomcat开发工程.html>
* <http://m.blog.csdn.net/lucas421634258/article/details/49908323>
