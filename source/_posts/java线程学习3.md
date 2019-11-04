---
title: java线程学习3-线程执行器与线程池
categories: 编程基础
---

### 前言

之前我们了解的线程都是自己实例化一个Thread类对象，然后在对其进行操作。

那其实，这样的话，需要人工地对线程进行维护，费时费力，而且出错风险也比较大，所以便有了线程池的这个概念的产生。

### 正题

线程池的作用不止是代替我们实例化，管理，执行，终止线程，而且提供了许多可供选择的特性。

要得到线程池，需要调用Executors类的静态方法如下：

* newCachedThreadPool
* newFixedThreadPool
* newScheduledThreadPool

这些方法返回的是一个实现了ExecutorService接口的对象。

ExecutorService

```
public interface ExecutorService extends Executor {
  void shutdown();
  
  List<Runnable> shutdownNow();
  
  boolean isShutdown();
  
  boolean isTerminated();
  
  boolean awaitTermination(long paramLong, TimeUnit paramTimeUnit) throws InterruptedException;
  
  <T> Future<T> submit(Callable<T> paramCallable);
  
  <T> Future<T> submit(Runnable paramRunnable, T paramT);
  
  Future<?> submit(Runnable paramRunnable);
  
  <T> List<Future<T>> invokeAll(Collection<? extends Callable<T>> paramCollection) throws InterruptedException;
  
  <T> List<Future<T>> invokeAll(Collection<? extends Callable<T>> paramCollection, long paramLong, TimeUnit paramTimeUnit) throws InterruptedException;
  
  <T> T invokeAny(Collection<? extends Callable<T>> paramCollection) throws InterruptedException, ExecutionException;
  
  <T> T invokeAny(Collection<? extends Callable<T>> paramCollection, long paramLong, TimeUnit paramTimeUnit) throws InterruptedException, ExecutionException, TimeoutException;
}
```

Executor

```
public interface Executor {
  void execute(Runnable paramRunnable);
}
```

由其上接口的定义可知，调用接口的话，可以使用submit，invokeAll或invokeAny。

然后传递的参数任务需要根据对应的方法实现Callable接口或Runnable接口。

最后的结果返回是一个Future类型的对象。

Future
```
public interface Future<V> {
  boolean cancel(boolean paramBoolean);
  
  boolean isCancelled();
  
  boolean isDone();
  
  V get() throws InterruptedException, ExecutionException;
  
  V get(long paramLong, TimeUnit paramTimeUnit) throws InterruptedException, ExecutionException, TimeoutException;
}
```

Callable

```
@FunctionalInterface
public interface Callable<V> {
  V call() throws Exception;
}
```

Runnable

```
@FunctionalInterface
public interface Runnable {
  void run();
}
```

如上所示，传递给线程池的任务需要继承Callable接口或Runnable接口。作为结果返回的Future接口类型的对象可以用get方法获取执行结果，cancel方法可以取消可取消执行的线程。

### 总结

上述有三种线程池，为的是减少线程创建的开销，控制线程的数量，还有有计划延迟地执行线程等。

主要的使用如上述接口的方法所示。便可以做到维护线程的创建，执行，销毁等。