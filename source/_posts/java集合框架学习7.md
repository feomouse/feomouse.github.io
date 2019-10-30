---
title: java集合框架学习7-Dictionary,Map,HashTable,HashMap
categories: java
---

### 前言

此次，我对数据结构-映射在哈希的存储与查找的概念下的实现进行了了解。

HashMap类是Map接口在添加哈希这个特点下的实现。

那Dictionary和HashTable呢？

### Map和Dictionary

刚开始学习集合框架的时候（也就是我写第一篇博文的时候），我其实是有些疑惑的。对于Map和Dictionary，我觉得都是对数据结构-键值对的接口定义，只不过Map是接口，Dictionary是抽象类，但是申明的方法操作，都是对基本的键值对数据结构的接口操作方法，简单的获取，移除，替换等操作，只不过Map接口定义的方法多了一些，复杂一些。

但重点是，他们的概念有重合，接口定义也有重合，那，为何？

唯一的解释，我觉得就只有用实现的时间不同来解释吧。Dictionary应该是早期的键值对数据结构的接口规则定义，而Map应该是后来，可能有更加复杂的需求产生，于是重新定义的一个接口。

所以基本上，由于Map接口更加完整，Dictionary现在是用不上了，用的更多的应该是Map下定义的方法还有实现类了。

### HashMap与HashTable

这两个类都是将哈希的特性添加到键值对数据结构的实现。所以和Map和Dictionary类似，都是有实现功能重合的。解释也只有一种，便是实现的时期不同，但为了兼容，而没有将先开发的HashTable类舍弃。但现在因该都用的是HashMap了。

那两者有啥不同呢？

##### 继承体系

HashTable

```
public class Hashtable<K, V>
  extends Dictionary<K, V>
  implements Map<K, V>, Cloneable, Serializable
```

HashMap

```
public class HashMap<K, V>
  extends AbstractMap<K, V>
  implements Map<K, V>, Cloneable, Serializable
```

从以上的继承看来，虽然，两者都实现了Map接口。可是HashTable是继承自Dictionary抽象类的，HashMap是继承自AbstractMap抽象类的。

1. 所以首先，HashMap有小部分方法的实现是委托给AbstractMap(因为其里面有一些方法的实现)。而为了实现Map接口，HashTable有许多方法是要自己实现的（毕竟Dictionary几乎和接口没什么区别，都只是方法的定义，并没有实现）。

2. HashTable内部储存键值对是定义了内部静态类Entry作为储存类型的。而HashMap则是使用了内部静态类Node作为储存类型的。

Entry

```
  private static class Entry<K, V>
    extends Object
    implements Map.Entry<K, V>
  {
    final int hash;
    final K key;
    V value;
    Entry<K, V> next;
    
    protected Entry(int param1Int, K param1K, V param1V, Entry<K, V> param1Entry) {
      this.hash = param1Int;
      this.key = param1K;
      this.value = param1V;
      this.next = param1Entry;
    }
    ......
    ......
```

Node

```
  static class Node<K, V>
    extends Object
    implements Map.Entry<K, V>
  {
    final int hash;
    final K key;
    V value;
    Node<K, V> next;
    
    Node(int param1Int, K param1K, V param1V, Node<K, V> param1Node) {
      this.hash = param1Int;
      this.key = param1K;
      this.value = param1V;
      this.next = param1Node;
    }
    
    public final K getKey() { return (K)this.key; }
    public final V getValue() { return (V)this.value; }
    public final String toString() { return this.key + "=" + this.value; }

    ......
    ......
```


3. HashTable对存储的话直接使用的是哈希，但HashMap内部还定义了一个TreeNode的内部类，作为存储，获取键值对的数据结构。也就是说，应该是在存储和获取的时候，存储在Node数组里，但其实存储的是将Node类型节点转换成TreeNode类型的节点后，按照树操作进行了优化。

TreeNode

```
  static final class TreeNode<K, V>
    extends LinkedHashMap.Entry<K, V>
  {
    TreeNode<K, V> parent;
    
    TreeNode<K, V> left;
    
    TreeNode<K, V> right;
    
    TreeNode<K, V> prev;
    
    boolean red;

    
    TreeNode(int param1Int, K param1K, V param1V, HashMap.Node<K, V> param1Node) { super(param1Int, param1K, param1V, param1Node); }

    ......
    ......
```

### 总结

当然，对HashTable和HashMap，从客户端的使用角度来说，都是基于Map接口的，所以具体类内部的实现，其实在使用集合类库的时候可以当成是黑箱，对键值对的操作还是Map接口里定义的方法。