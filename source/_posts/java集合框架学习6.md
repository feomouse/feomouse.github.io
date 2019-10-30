---
title: java集合框架学习6-Set，SortedSet，NavigableSet，AbstractSet，TreeSet
categories: java
---

### 前言

上一篇我描写了TreeMap的所实现的特点，接口方法等。那这次，同样的，在Set数据结构而言，java集合框架实现了一个几乎类似的继承体系和类实现。

### 继承体系

Set

```
public interface Set<E>
  extends Collection<E>
```

SortedSet

```
public interface SortedSet<E>
  extends Set<E>
```

NavigableSet

```
public interface NavigableSet<E> extends SortedSet<E>
```

TreeSet

```
public class TreeSet<E>
  extends AbstractSet<E>
  implements NavigableSet<E>, Cloneable, Serializable
```

如上所示，和TreeMap实现的思路类似，都是：

* SortedSet接口拓展Set接口，添加有序的特点；
* NavigableSet接口拓展SortedSet接口，添加可导航的特点；
* TreeSet类继承自AbstractSet抽象类，并实现了NavigableSet接口

### SortedSet

```
  Comparator<? super E> comparator();
  
  SortedSet<E> subSet(E paramE1, E paramE2);
  
  SortedSet<E> headSet(E paramE);
  
  SortedSet<E> tailSet(E paramE);
  
  E first();
  
  E last();
  
  default Spliterator<E> spliterator() {
    return new Spliterators.IteratorSpliterator<E>(this, 21)
      {
        public Comparator<? super E> getComparator()
        {
          return SortedSet.this.comparator();
        }
      };
  }
```

如上接口的定义所示，在基础的Set接口的定义之下，添加了一些特点：

* 元素比较
* 根据有序元素获取子集
* 根据有序元素获取比其大或比其小的子集
* 获取首尾元素

因为有了排序元素这个概念，所以才有了以上的接口方法的定义。

### NavigableSet

```
  E lower(E paramE);
  
  E floor(E paramE);
  
  E ceiling(E paramE);
  
  E higher(E paramE);
  
  E pollFirst();
  
  E pollLast();
  
  Iterator<E> iterator();
  
  NavigableSet<E> descendingSet();
  
  Iterator<E> descendingIterator();
  
  NavigableSet<E> subSet(E paramE1, boolean paramBoolean1, E paramE2, boolean paramBoolean2);
  
  NavigableSet<E> headSet(E paramE, boolean paramBoolean);
  
  NavigableSet<E> tailSet(E paramE, boolean paramBoolean);
  
  SortedSet<E> subSet(E paramE1, E paramE2);
  
  SortedSet<E> headSet(E paramE);
  
  SortedSet<E> tailSet(E paramE);
```

以上是继承自SortedSet接口的NavigableSet接口的实现。

在集合元素有序的前提下，我们可以有大小或前后的概念，所以我们可以通过一些接口方法实现一些添加的方法定义：

* 获取比指定元素小（大）的最大（小）的元素
* 返回可迭代的集合，逆序的集合
* 返回比指定元素小（大）的集合的子集

### TreeSet

TreeSet类里实现的公有方法和Set接口，SortedSet接口，NavigableSet接口里定义的方法一致。

值得注意的是，TreeSet里保存了一个NavigableMap接口类型的变量。而且没有什么其他的集合变量，所以我会认为，TreeSet在树结构的实现上是依托于TreeMap的实现的。

### 总结

此次我根据java集合框架里，对tree的概念应用在Map和Set上，进行了认知，虽说没有具体提及tree的实现细节，但是对其实现的功能，也就是接口定义来说，有了一个大致的了解。

那么在客户端使用时，对指定操作的集合需要添加一些特性，比如有序，导航等需求，则可以使用TreeSet来实现。