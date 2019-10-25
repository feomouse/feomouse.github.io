---
title: java集合框架学习1
categories: java
---

### 前言

数据结构，从计算机诞生之日便存在，其最直接的存在原因便是储存数据，并方便对存储的数据进行操作，从而实现业务需求。

现代高级编程语言，特别是在面向对象语言中，配套的数据结构实现已经是必然。java这门面向对象的高级语言也是。通过面向对象的结构，抽象类，和类继承，封装，多态概念，从而实现了对不同概念的数据结构的封装。

### 存在的数据结构

##### 存在的形容词或名词

* Abstract
* Collection
* List
* Map
* Queue
* Sequential
* Set
* Deque
* Linked
* Array
* Base64
* Bit
* Calendar
* Comparable
* Comparator
* Currency
* Date
* Dictionary
* Enum
* Event
* Formattable
* formatter
* table
* Identity
* Hash
* Iterator
* Navigable
* Obervable
* Oberver
* Optional
* Primitive
* Property
* Permission
* Resource
* Bundle
* Random
* Access
* Sub
* Scanner
* ServiceLoader
* TimeZone
* Sorted
* Spliterator
* Stack
* Joiner
* Tokenizer
* Task
* Tree
* UUID
* Vector
* Weak

以上是我从java.util里提炼出来的，关于集合类库的形容词或名词。

有一些在数据结构里比较常见的存在，我想列出：

* Collection
* List
* Map
* Queue
* Set
* Deque
* Linked
* Array
* Comparable
* Comparator
* Dictionary
* Enum
* table
* Hash
* Iterator
* Property
* Sub
* Sorted
* Stack
* Tree
* Vector

以上这些数据结构的概念，有名词：集合（Collection，Set），列表（List），映射（Map），队列（Queue），数组（Array），字典（Dictionary），栈（Stack），树（Tree）等。

其中除了栈和数和数组，其他都有相对应的接口定义。

而数组和树在这其实是个形容词概念。栈虽然是个继承自Vector的具体类，但是其自身定义也是跟简单，也只有push，pop，peek，empty，search这几个方法的简单实现。

所以在现阶段，对java集合框架的大体基础理解，可以从以上列出了数据结构中的，名词概念的接口开始入手学习与研究。

然后还有一些形容的：链接，对比，枚举，哈希，迭代，子集，树，排序等，都是在运用或实现数据结构时的一些概念。这些概念可以充当特性添加到以上基本数据结构名词当中，作为具体类的实现以拓展数据结构接口。

所以，下一次，会从这几个最基础的接口开始研究。

### 总结

当然，在我列出的集合框架的全部存在中，有很多我刚才讨论时是没有提到的，占了至少多余五成。其实纯属小人不才，在没遇到具体的应用场景之前，不敢往下断论。

而我觉得解决我已经讨论的内容，对于平时应用java数据结构来编写业务代码或者是普通算法时，已经足够用了。