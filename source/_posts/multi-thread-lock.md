---
title: Synchronized和Lock的区别
date: 2021-02-15 13:04:13
tags: [Java,Thread]
---

1. ##### 原始构成

   1. Synchronized是关键字属于JVM层面 底层使用monitor（monitorenter，monitorexit）
   2. Lock是具体类（Java.util.concurrent.locks.lock）是API层面的锁

2. ##### 使用方法

   1. synchronized不需要用户去手动释放锁，当 synchronized代码执行完后系统会自动让线程释放对锁的占用
   2. ReentrantLock则需要用户去手动释放锁若没有主动释放锁，就有可能导致出现死锁现象。需要Lock()和 unlock()方法结合try/finally语句块来完成。

3. ##### 等待是否可中断

   1. synchronized不可中断，除作抛出异常或者正常运行完成
   2. ReentrantLock可中断，
      1. 设置超时方法 tryLock( Long timeout， TimeUnit unit)
      2. LockInterruptibly()放代码块中，调 Interrupt()方法可中断

4. ##### 加锁是否公平

   1. synchronized非公平锁
   2. ReentrantLock两者部可以，默认非公平锁，道方法可以传入boolean值，true为公平锁，false为非公平锁

5. ##### 锁绑定多个条件Condition

   1. synchronized没有
   2. ReentrantLock用来实现分组唤醒需要唤醒的线程们，可以精确唤醒，而不是像 synchronized要么随机唤醒一个线程 要么唤醒全部线程。

   



