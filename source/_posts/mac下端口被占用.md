---
title: mac下端口被占用
date: 2017-12-27 17:54:33
tags: shell
categories:
---

mac 系统下查看 8080 端口是否被占用：

```shell
lsof -i:8080
```

结果显示：

```shell
COMMAND   PID     USER   FD   TYPE             DEVICE SIZE/OFF NODE NAME
java    93394 huangwei   44u  IPv6 0x1261f7a7972f904b      0t0  TCP *:http-alt (LISTEN)
```

看到一个 java 进程占用了 8080 端口。关闭该进程：

```shell
kill -9 93394
```
