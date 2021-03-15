---
title: ThreadLocal
date: 2021-01-12 11:39:39
tags: Java
---


## 描述

ThreadLocal能够允许我们存储只能被特定线程访问的数据



## ThreadLocal接口

以存储Integer类型的数据为例，首先创建一个ThreadLocal

```java
ThreadLocal<Integer> threadLocalValue = new ThreadLocal<>();
```

可以通过**get()**或**set()**方法来操作这个值，我们可认为ThreadLocal是一个以线程作为Key 存储的数据作为Value的一个Map

```java
threadLocalValue.set(1);
Integer result = threadLocalValue.get();
```

也可以通过传一个Supplier给**withinitial()**这个静态方法来构造一个ThreadLocal

```java
ThreadLocal<Integer> threadLocal = ThreadLocal.withInitial(() -> 1);
```

使用remove()来删除这个值

```java
threadLocal.remove();
```



## ThreadLocal与ThreadPool

当我们将ThreadLocal与ThreadPool放在一起使用时需要格外的注意

1. 首先应用从线程池中借用了一个线程
2. 然后这个线程存储了与线程密切相关的值在当前线程中的ThreadLocal里
3. 当前这个线程执行结束后，应用归还了这个线程
4. 过了一会，应用又借用了一个进程用来处理另一个请求
5. 如果说该应用上一次在使用该线程时没有做清理，那么在这一次处理新的请求的时候，就会再一次使用老数据来处理新请求

这样就会在多线程应用中导致非常严重的问题



## 扩展ThreadPoolExecutor

可以通过提供一个自定义的Hook在线程池中的线程执行任务的前后分别执行 **beforeExecute()** 和 **afterExecute()** 方法

这样我们可以在**afterExecute()**方法中执行ThreadLocal的**remove()**方法

```java
public class ThreadLocalAwareThreadPool extends ThreadPoolExecutor {

    @Override
    protected void afterExecute(Runnable r, Throwable t) {
        // 调用每个ThreadLocal的remove方法
    }
}
```



> Ref:https://www.baeldung.com/java-threadlocal