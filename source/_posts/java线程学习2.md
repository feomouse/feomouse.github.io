---
title: java线程学习2
categories: java
---

### 前言

前一篇文章我介绍了最最基础的线程。现在我想介绍线程间的协调，以完成一些多线程并发执行任务。

### 方法

* synchronized
* wait方法与notify方法与notifyAll方法
* ReetrantLock类实例的lock和unlock方法
* ReetrantReadWriteLock类实例的readLock().lock和unLock方法,writeLock().lock和unLock方法
* ReetrantLock类实例的newCondition方法返回的Condition对象上的await和signal和signalAll方法
* Semaphore信号量类实例的require和release方法

其实大体上都是几个固定的概念：

* 防止重入执行区域
* 防止对资源的非法访问，无序更改