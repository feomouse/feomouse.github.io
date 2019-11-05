---
title: java集合框架-LinkedHashSet与LinkedHashMap
categories: 数据结构与算法
---

### 前言

之前已经了解过HashSet和HashMap数据结构。也了解过Linked特性。那这次我来简单了解一下LinkedHashSet和LinkedHashMap的实现。

### 继承结构

```
public class LinkedHashSet<E>
  extends HashSet<E>
  implements Set<E>, Cloneable, Serializable

public class LinkedHashMap<K, V>
  extends HashMap<K, V>
  implements Map<K, V>
```

以上是源码中定义的继承结构。对于两者都是实现了对应数据结构的接口，而且都继承自哈希的具体实现。那么继续深入了解一下。

### LinkedHashSet

```
public class LinkedHashSet<E>
  extends HashSet<E>
  implements Set<E>, Cloneable, Serializable
{
  private static final long serialVersionUID = -2851667679971038690L;
  
  public LinkedHashSet(int paramInt, float paramFloat) { super(paramInt, paramFloat, true); }

  public LinkedHashSet(int paramInt) { super(paramInt, 0.75F, true); }

  public LinkedHashSet() { super(16, 0.75F, true); }

  public LinkedHashSet(Collection<? extends E> paramCollection) {
    super(Math.max(2 * paramCollection.size(), 11), 0.75F, true);
    addAll(paramCollection);
  }
  
  public Spliterator<E> spliterator() { return Spliterators.spliterator(this, 17); }
}
```

LinkedHashSet的源码不多，于是我全部都贴了出来。很明显，其几乎所有的操作都委托给了继承体系中的父类HashSet。而HashSet之前我们已经了解过对数据的操作是将操作委托给了HashMap数据结构（以前写的博文了解过）。

### LinkedHashMap

对于此类，看了源代码，有几点：

1. 其自身也只保存了很简单的几个数据字段，也就是说，Map数据的主体并不是保存在其自身的；

```
  private static final long serialVersionUID = 3801124242820219131L;
  Entry<K, V> head;
  Entry<K, V> tail;
  final boolean accessOrder;

  static class Entry<K, V>
    extends HashMap.Node<K, V>
  {
    Entry<K, V> before;
    Entry<K, V> after;
    
    Entry(int param1Int, K param1K, V param1V, HashMap.Node<K, V> param1Node) { super(param1Int, param1K, param1V, param1Node); }
  }
```

如上述的源码字段，其数据结构只保存了head和tail字段。也就是说其提供了Linked概念的实现，并定义了个内部类Entry作为实现Linked概念的帮助。而从上面的Entry类的定义，可见其是继承自HashMap类中的内部类Node。如下：

```
  static class Node<K, V>
    extends Object
    implements Map.Entry<K, V>
  {
    final int hash;
    final K key;
    V value;
    Node<K, V> next;

  ......
```

也就是说在存储数据结构的时候，其在HashMap存储数据结构的类型Node的基础上，添加了链表所拥有的前后节点的概念。于是构成了一个新的存储结构Entry，其不仅拥有HashMap的Node存储特性，而且也有链表特性。所以数据的保存由原来的Node类型，转变成了Entry类型。

2. 大量的操作方法具体实现都委托给了父类。

我看LinkedHashMap类的内部大量的操作都委托给了父类。也就是说，没重写的方法都是直接调用的父类的方法。而重写了的方法则很多逻辑都是委托给了父类。那么，如果委托给了父类，那其自身保持的数据结构是如何操作的呢？

再仔细了解，我们发现LinkedHashMap重写了一些方法。

```
  void reinitialize() {
    super.reinitialize();
    this.head = this.tail = null;
  }
  
  HashMap.Node<K, V> newNode(int paramInt, K paramK, V paramV, HashMap.Node<K, V> paramNode) {
    Entry entry = new Entry(paramInt, paramK, paramV, paramNode);
    
    linkNodeLast(entry);
    return entry;
  }
  
  HashMap.Node<K, V> replacementNode(HashMap.Node<K, V> paramNode1, HashMap.Node<K, V> paramNode2) {
    Entry entry1 = (Entry)paramNode1;
    Entry entry2 = new Entry(entry1.hash, entry1.key, entry1.value, paramNode2);
    
    transferLinks(entry1, entry2);
    return entry2;
  }
  
  HashMap.TreeNode<K, V> newTreeNode(int paramInt, K paramK, V paramV, HashMap.Node<K, V> paramNode) {
    HashMap.TreeNode treeNode = new HashMap.TreeNode(paramInt, paramK, paramV, paramNode);
    linkNodeLast(treeNode);
    return treeNode;
  }
  
  HashMap.TreeNode<K, V> replacementTreeNode(HashMap.Node<K, V> paramNode1, HashMap.Node<K, V> paramNode2) {
    Entry entry = (Entry)paramNode1;
    HashMap.TreeNode treeNode = new HashMap.TreeNode(entry.hash, entry.key, entry.value, paramNode2);
    transferLinks(entry, treeNode);
    return treeNode;
  }

  void afterNodeRemoval(HashMap.Node<K, V> paramNode) {
    Entry entry1 = (Entry)paramNode;
    Entry entry2 = entry1.before, entry3 = entry1.after;
    entry1.before = entry1.after = null;
    if (entry2 == null) {
      this.head = entry3;
    } else {
      entry2.after = entry3;
    }  if (entry3 == null) {
      this.tail = entry2;
    } else {
      entry3.before = entry2;
    } 
  }
  void afterNodeInsertion(boolean paramBoolean) {
    Entry entry;
    if (paramBoolean && (entry = this.head) != null && removeEldestEntry(entry)) {
      Object object = entry.key;
      removeNode(hash(object), object, null, false, true);
    } 
  }
  
  void afterNodeAccess(HashMap.Node<K, V> paramNode) {
    Entry entry;
    if (this.accessOrder && (entry = this.tail) != paramNode) {
      Entry entry1 = (Entry)paramNode;
      Entry entry2 = entry1.before, entry3 = entry1.after;
      entry1.after = null;
      if (entry2 == null) {
        this.head = entry3;
      } else {
        entry2.after = entry3;
      }  if (entry3 != null) {
        entry3.before = entry2;
      } else {
        entry = entry2;
      }  if (entry == null) {
        this.head = entry1;
      } else {
        entry1.before = entry;
        entry.after = entry1;
      } 
      this.tail = entry1;
      this.modCount++;
    } 
  }
  
  void internalWriteEntries(ObjectOutputStream paramObjectOutputStream) throws IOException {
    for (Entry entry = this.head; entry != null; entry = entry.after) {
      paramObjectOutputStream.writeObject(entry.key);
      paramObjectOutputStream.writeObject(entry.value);
    } 
  }

  private void linkNodeLast(Entry<K, V> paramEntry) {
    Entry entry = this.tail;
    this.tail = paramEntry;
    if (entry == null) {
      this.head = paramEntry;
    } else {
      paramEntry.before = entry;
      entry.after = paramEntry;
    } 
  }

  private void transferLinks(Entry<K, V> paramEntry1, Entry<K, V> paramEntry2) {
    Entry entry1 = paramEntry2.before = paramEntry1.before;
    Entry entry2 = paramEntry2.after = paramEntry1.after;
    if (entry1 == null) {
      this.head = paramEntry2;
    } else {
      entry1.after = paramEntry2;
    }  if (entry2 == null) {
      this.tail = paramEntry2;
    } else {
      entry2.before = paramEntry2;
    } 
  }
```

以上这些方法，我们可以从源码中得知，HashMap类中也有对这些方法的定义：

```
  void reinitialize() {
    this.table = null;
    this.entrySet = null;
    this.keySet = null;
    this.values = null;
    this.modCount = 0;
    this.threshold = 0;
    this.size = 0;
  }

  Node<K, V> newNode(int paramInt, K paramK, V paramV, Node<K, V> paramNode) { return new Node(paramInt, paramK, paramV, paramNode); }

  Node<K, V> replacementNode(Node<K, V> paramNode1, Node<K, V> paramNode2) { return new Node(paramNode1.hash, paramNode1.key, paramNode1.value, paramNode2); }

  TreeNode<K, V> newTreeNode(int paramInt, K paramK, V paramV, Node<K, V> paramNode) { return new TreeNode(paramInt, paramK, paramV, paramNode); }

  TreeNode<K, V> replacementTreeNode(Node<K, V> paramNode1, Node<K, V> paramNode2) { return new TreeNode(paramNode1.hash, paramNode1.key, paramNode1.value, paramNode2); }
  
  void afterNodeAccess(Node<K, V> paramNode) {}
  
  void afterNodeInsertion(boolean paramBoolean) {}
  
  void afterNodeRemoval(Node<K, V> paramNode) {}
  
  void internalWriteEntries(ObjectOutputStream paramObjectOutputStream) throws IOException {
    Node[] arrayOfNode;
    if (this.size > 0 && (arrayOfNode = this.table) != null) {
      for (byte b = 0; b < arrayOfNode.length; b++) {
        for (Node node = arrayOfNode[b]; node != null; node = node.next) {
          paramObjectOutputStream.writeObject(node.key);
          paramObjectOutputStream.writeObject(node.value);
        } 
      } 
    }
  }
```

也就是说，LinkedHashMap通过对这些方法的重写，虽然讲大部分逻辑都委托给了父类HashMap，但是也可以实现对自身保持的变量head，tail的操作。

### 总结

就如类名所示，LinkedHashMap和LinkedHashSet都是添加了哈希和链表特性的数据结构接口Map和Set的操作。但是对于在面向对象中的设计，以及对已有代码的利用，是此次学习的重点。