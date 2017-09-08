---
title: Java动态代理
date: 2017-09-04 15:01:54
tags:
categories:
---

有了动态代理，对应的也就有静态代理。当然首先明确什么是代理。


## 代理

代理，或者叫proxy，简单理解就是事情我不用去做，由其他人来替我完成。举例说明：赚钱方面，我就是我老婆的代理；带小孩方面，我老婆就是我的代理；家务方面，没有代理。

放在计算机术语当中，就属于代理模式。为其他对象提供一种代理以控制对这个对象的访问。在某些情况下，一个客户不想或者不能直接引用另一个对象，而代理对象可以在客户端和目标对象之间起到中介的作用。


## 静态代理

```java
public interface ISubject {
    public void doSomething();
}

public class Subject implements ISubject {
    @Override
    public void doSomething() {
        System.out.println("call doSomething()");
    }
}

public class ISubjectProxy implements ISubject {
    private Subject subject = new Subject();

    @Override
    public void doSomething() {
        subject.doSomething();
    }

    public static void main(String[] args) {
        ISubject subject = new ISubjectProxy();
        subject.doSomething();
    }
}
```

此时ISubjectProxy就是对Subject的代理。这样做的好处是，可以对真实调用类进行封装，**选择性的暴露接口**，也可以**对调用的过程进行监控**。比如可以对所有第三方服务的调用都加上代理，对第三方服务进行调用前后的日志输出监控，以及其他参数（调用次数等）的监控。

但是静态代理有个缺点，当代理的方法越来越多时，代理量也会增加。所以需要引入动态代理来解决此类问题。


## 动态代理

```java
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

public class ProxyHandler implements InvocationHandler {
    private Object tar;

    @Override
    public Object invoke(Object o, Method method, Object[] objects) throws Throwable {
        Object result;
        System.out.println("call before");
        result = method.invoke(tar, objects);
        System.out.println("call after");
        return result;
    }

    public Object bind(Object tar) {
        this.tar = tar;
        return Proxy.newProxyInstance(tar.getClass().getClassLoader(), tar.getClass().getInterfaces(), this);
    }

    public static void main(String[] args) {
        ProxyHandler proxy = new ProxyHandler();
        ISubject subject = (ISubject) proxy.bind(new Subject());
        subject.doSomething();
    }
}
```

```
// output
call before
call doSomething()
call after
```

动态代理的好处就是proxy类的代码量固定下来了，不会因为业务的增长而增长。可以实现AOP编程。

但是无论是静态代理还是动态代理，它都需要一个接口。如果想要包装的方法不是接口，就没办法了。只能用CGLib动态代理了。


## CGLib动态代理




## 问题

为什么java的动态代理只能对interface进行操作？


## 参考
- http://www.360doc.com/content/14/0801/14/1073512_398598312.shtml
- http://blog.csdn.net/mhmyqn/article/details/48474815

















