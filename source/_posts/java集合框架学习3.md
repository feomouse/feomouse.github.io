---
title: java集合框架-接口或抽象类Map,Dictionary,Stack
categories: 数据结构与算法
---

### Map

Map接口相较于之前的其他接口，其多了个键的概念。也就是说，其不再继承自Collection接口。以下是其定义的概念：

* 大小
* 获取，得到，去除键和值
* 清除全部键和值
* 以及一些默认方法的实现，如下：

```
  default V getOrDefault(Object paramObject, V paramV)
  default void forEach(BiConsumer<? super K, ? super V> paramBiConsumer)
  default void replaceAll(BiFunction<? super K, ? super V, ? extends V> paramBiFunction)
  default V putIfAbsent(K paramK, V paramV)
  default boolean remove(Object paramObject1, Object paramObject2)
  default boolean replace(K paramK, V paramV1, V paramV2)
  default V replace(K paramK, V paramV)
  default V computeIfAbsent(K paramK, Function<? super K, ? extends V> paramFunction)
  default V computeIfPresent(K paramK, BiFunction<? super K, ? super V, ? extends V> paramBiFunction)
  default V compute(K paramK, BiFunction<? super K, ? super V, ? extends V> paramBiFunction)
  default V merge(K paramK, V paramV, BiFunction<? super V, ? super V, ? extends V> paramBiFunction)
```

还有，最主要的其中定义了一个Entry的内置接口，其主要可以用来：

* 对比值
* 获取设置键
* 设置值

```
  public static interface Entry<K, V>
  {
    static <K extends Comparable<? super K>, V> Comparator<Entry<K, V>> comparingByKey() {
      ......
    }

    static <K, V extends Comparable<? super V>> Comparator<Entry<K, V>> comparingByValue() {
      ......
    }

    static <K, V> Comparator<Entry<K, V>> comparingByKey(Comparator<? super K> param1Comparator) {
      ......
    }

    static <K, V> Comparator<Entry<K, V>> comparingByValue(Comparator<? super V> param1Comparator) {
      ......
    }

    K getKey();

    V getValue();

    V setValue(V param1V);

    boolean equals(Object param1Object);

    int hashCode();
  }
```

### Dictionary

这个是以抽象类的形式定义的，其下的全部方法都是定义为抽象方法，没有实现，所以其实和定义为一个接口的差别不大，可能最主要的原因是在框架类库设计的时候，作者没想吧Dictionary当成一个向用户曝露的接口，只想当成内部的逻辑实现。

具体定义如下：

```
  public abstract int size();
  
  public abstract boolean isEmpty();
  
  public abstract Enumeration<K> keys();
  
  public abstract Enumeration<V> elements();
  
  public abstract V get(Object paramObject);
  
  public abstract V put(K paramK, V paramV);
  
  public abstract V remove(Object paramObject);
```

如上，主要的接口作用总结起来有：

* 大小
* 迭代键或值
* 获取值，设置值，清除值

### Stack

这个是一个具体的类，继承自Vector类（之后我会专门讨论），而Vector的类如下：

```
public class Vector<E>
  extends AbstractList<E>
  implements List<E>, RandomAccess, Cloneable, Serializable
  ...
```

所以，stack栈类其最基本是实现了Vector里的方法，也就是作为List，还有一些接口比如RandomAccess，Cloneable，Serializable的实现方法等。

在此基础上，其定义了栈最为熟悉的push，pop，peek方法，还有empty，search方法等。

* 插入值，删除值，获得值
* 空判断
* 查找值

### 总结

此次虽说介绍的有接口，有抽象类，有具体类，但是都是属于比较顶层的类或接口定义或实现，也是比较基础的。