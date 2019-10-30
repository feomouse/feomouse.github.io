---
title: java集合框架学习5-Map，SortedMap，NavigableMap，AbstractMap，TreeMap
categories: java
---

### 前言

Map接口和对应的AbstractMap抽象类在以前的文章中我已大致讲过。今天这篇文章是由于想要认识TreeMap而写的。

### 继承体系

TreeMap：

```
public class TreeMap<K, V>
  extends AbstractMap<K, V>
  implements NavigableMap<K, V>, Cloneable, Serializable
```

NavigableMap：

```
public interface NavigableMap<K, V> extends SortedMap<K, V> 
```

SortedMap：

```
public interface SortedMap<K, V> extends Map<K, V>
```

AbstractMap：

```
public abstract class AbstractMap<K, V>
  extends Object
  implements Map<K, V>
```

以上是源码里的继承体系，其实从类名便可以得知每一个接口与类的含义和作用与特点。

* SortedMap便是具有排序特点的映射接口定义；
* NavigableMap便是在有序的特点的映射的基础上具有导航特点的映射接口定义；
* TreeMap类是集齐基本的抽象映射类实现，排序映射，可导航映射接口方法的实现；

### SortedMap

这个接口很明显是为了让映射具有排序特点而诞生的。其添加的特性有：

* 根据键值指定映射子集
* 头尾映射
* 头尾键值
* 键值比较

```
  Comparator<? super K> comparator();
  
  SortedMap<K, V> subMap(K paramK1, K paramK2);
  
  SortedMap<K, V> headMap(K paramK);
  
  SortedMap<K, V> tailMap(K paramK);
  
  K firstKey();
  
  K lastKey();
  
  Set<K> keySet();
  
  Collection<V> values();
  
  Set<Map.Entry<K, V>> entrySet();
```

从以上接口定义我们不难看出，最突出的便是对键值的可比较性带来的映射数据结构的先后概念。从而实现了排序的概念。

### NaviableMap

这个接口是在映射数据结构已经具有有序这个特点的情况下，对映射数据相比SortedMap接口有更多定义的操作。

比如返回比指定值都大或都小的映射，返回最前或最后的映射，或返回倒排序的映射等。

```
  Map.Entry<K, V> lowerEntry(K paramK);
  
  K lowerKey(K paramK);
  
  Map.Entry<K, V> floorEntry(K paramK);
  
  K floorKey(K paramK);
  
  Map.Entry<K, V> ceilingEntry(K paramK);
  
  K ceilingKey(K paramK);
  
  Map.Entry<K, V> higherEntry(K paramK);
  
  K higherKey(K paramK);
  
  Map.Entry<K, V> firstEntry();
  
  Map.Entry<K, V> lastEntry();
  
  Map.Entry<K, V> pollFirstEntry();
  
  Map.Entry<K, V> pollLastEntry();
  
  NavigableMap<K, V> descendingMap();
  
  NavigableSet<K> navigableKeySet();
  
  NavigableSet<K> descendingKeySet();
  
  NavigableMap<K, V> subMap(K paramK1, boolean paramBoolean1, K paramK2, boolean paramBoolean2);
  
  NavigableMap<K, V> headMap(K paramK, boolean paramBoolean);
  
  NavigableMap<K, V> tailMap(K paramK, boolean paramBoolean);
  
  SortedMap<K, V> subMap(K paramK1, K paramK2);
  
  SortedMap<K, V> headMap(K paramK);
  
  SortedMap<K, V> tailMap(K paramK);
```

不管怎样，都是在映射已经具有排序特点的情况下，添加了更多的操作，使数据结构使用起来更加方便。

### TreeMap

我观察了以下，树实现映射，在公有接口上，几乎都和在Map，SortedMap，NavigableMap接口里的方法定义匹配。

所以其实可以把其当成实现了映射，排序和可导航特点的数据结构。那Tree呢？怎么体现Tree呢？

其实可以把树这个概念当成其内部概念，也就是说，在使用的时候，我们并不会和Tree这个概念打交道，Tree这个概念是其实现映射方法的概念。就好像这个类是个黑箱，而我们用户在使用这个类提供的数据结构方法时，其实只需要意识到其是一个映射，而且具有有序，可导航等特点的映射就可以了。

TreeMap这个类的内部实现比较复杂，这次我也没打算跟你们分析这方面，因为有大量的内部类和私有方法的实现，其体现了树这个概念。

而树这种数据结构在大学里教的时候，更多的是运用在提升类似算法的效率，降低其时间复杂度和空间复杂度等角度上去认知与理解的。其本身更多是抽象概念上的，具体在计算机里的实现其实即可用数组或链表实现。

### 总结

java的集合框架包含了许多基础的数据结构，包括映射。对于映射的定义，其实是大同小异的，在此数据结构上添加一些特性，如可排序等，也是无可厚非的。

但是就实现来说，在不同的情况下，可以运用不同的思想，比如将树和映射结合，以将树的一些优点运用到基础数据结构的实现当中，也是java集合框架的特点之一。