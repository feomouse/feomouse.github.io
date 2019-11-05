---
title: java集合框架-接口Collection,Set,List,Queue,Deque
categories: 数据结构与算法
---

### 前言

我之前一篇提到过，框架里的大体数据结构实体概念都有对应接口定义。今天我来大体了解其中一些。

### Collection

代码我就不贴了。

其中对他大体的了解，围绕的有以下接口：

* 数据结构结构存储元素的大小（size,isEmpty）；
* 迭代定义（iterator）；
* 转换成数组（toArray非泛型和泛型版本）；
* 添加去除单个或多个元素（add,remove,addAll,removeAll,removeIf）；
* 保留指定集合元素（retainAll）；
* 判断是否包含的指定元素（contains，containsAll）；
* 清除全部元素（clear）；

以上是基本的定义，但还有：

```
  boolean equals(Object paramObject);

  int hashCode();

  default Spliterator<E> spliterator() { return Spliterators.spliterator(this, 0); }

  default Stream<E> stream() { return StreamSupport.stream(spliterator(), false); }

  default Stream<E> parallelStream() { return StreamSupport.stream(spliterator(), true); }
```

等接口定义，占时先不讨论，因为涉及到数据结构外的知识。

### Set

Set接口继承自Collection接口，定义的接口方法全部几乎和Collection接口一样，知识参数有些许不同，除了少了以下两个接口

```
  default Stream<E> stream() { return StreamSupport.stream(spliterator(), false); }

  default Stream<E> parallelStream() { return StreamSupport.stream(spliterator(), true); }
```

的重新定义，其他几乎都是覆盖定义了Collection接口的方法。

### List

List接口也是继承了Collection接口，大多数基本方法也和Collection接口定义一样。但是有以下新添加的接口方法。

```
  default void replaceAll(UnaryOperator<E> paramUnaryOperator)
  default void sort(Comparator<? super E> paramComparator);
  E get(int paramInt);
  E set(int paramInt, E paramE);
  void add(int paramInt, E paramE);
  E remove(int paramInt);
  int indexOf(Object paramObject);
  int lastIndexOf(Object paramObject);
  ListIterator<E> listIterator();
  ListIterator<E> listIterator(int paramInt);
  List<E> subList(int paramInt1, int paramInt2);
```
添加的具体公用如下。

* 替换
* 排序
* 根据引索存取数据
* 根据数据获取引索
* 获取子列表
* 获取列表迭代器

列表迭代器ListIterator继承自Iterator接口，而且自身还添加了一些接口方法。

```
  boolean hasPrevious();
  E previous();
  int nextIndex();
  int previousIndex();
  void set(E paramE);
  void add(E paramE);
```

可以看见，列表迭代器接口相比较于迭代器接口，其有了前后位置的概念，也有了引索的概念。还可以对迭代器的位置元素进行放置。

### Queue

Queue接口继承自Collection接口，提供了的基础功能如下：

* 插入（offer）
* 检索并清除（poll，remove）
* 检索并不清除（peek，element）

### Deque

Deque接口继承自Queue接口。其在Queue接口的概念上，添加了一些概念。

* 第一和最后的位置
* push和pop
* 大小
* 迭代器
* 包含判断
* 去除指定元素

在以上添加的概念中，就好似在队列的基础上添加了双向的这个概念。也就是说，队头和队尾都可以执行插入和删除操作。