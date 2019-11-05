---
title: java集合框架-ArrayList与LinkedList
categories: 数据结构与算法
---

### 前言

在之前，我们已经了解了集合框架基本的接口，其中包括List接口。那这次我看了以下对List接口进行实现的具体类，那么其中就有ArrayList和LinkedList两种。

### 继承体系

以下是ArrayList和LinkedList的继承体系。

ArrayList

```
public class ArrayList<E>
  extends AbstractList<E>
  implements List<E>, RandomAccess, Cloneable, Serializable
```

LinkedList

```
public class LinkedList<E>
  extends AbstractSequentialList<E>
  implements List<E>, Deque<E>, Cloneable, Serializable
```

由以上可知，这两个具体实现类有一些共同点：都实现了List，Cloneable，Serializable接口。

但是不同点便是：

1. 在实现的接口中，ArrayList实现了一个RandomAccess接口，而LinkedList没有实现这个接口，但是实现了Deque接口。

```
public interface RandomAccess {}
```

从以上的定义看来，RandomAccess接口没有定义任何方法，那么其是个标识接口，说明ArrayList具有随机访问的特性，而LinkedList则没有随机访问的特性。

但是，LinkedList可以像双向队列一样操作，所以实现了Deque接口。这个接口我们之前在讨论接口的时候已经了解了，就不再说明。

2. ArrayList继承自AbstractList抽象类，而LinkedList继承自AbstractSequentialList抽象类。

在之前讲解抽象类的时候，我们应该已经了解过，AbstractSequentialList抽象类继承自AbstractList抽象类，AbstractList实现了List接口。在功能上，AbstractSequentialList添加了序列化的特性，于是在获取添加设置删除等操作有了更具体的实现。

那么ArrayList继承自AbstractList，而LinkedList继承自AbstractSequentialList，从语义上很明显地得知，ArrayList没有序列化的特性，所以之前才有个RandomAccess接口的实现。而LinkedList有序列化的特性，所以也有顺序的概念，所以才实现了Deque接口。

### 大概的具体差别

从实现看来，这两个类的Array和Linked的差别和以前上的数据结构课的概念上是一致的。Array就只是一个单纯的，用下标可以访问的列表。但是Link则加入了链表的概念，所以也有c或c++概念中的指针来表明节点间的前后关系。

给个例子：

##### ArrayList

```
  ......
  Object[] elementData;

  private int size;

  public E get(int paramInt) {
    rangeCheck(paramInt);
    
    return (E)elementData(paramInt);
  }

  E elementData(int paramInt) { return (E)this.elementData[paramInt]; }

  public E set(int paramInt, E paramE) {
    rangeCheck(paramInt);
    
    Object object = elementData(paramInt);
    this.elementData[paramInt] = paramE;
    return (E)object;
  }
  
  public boolean add(E paramE) {
    ensureCapacityInternal(this.size + 1);
    this.elementData[this.size++] = paramE;
    return true;
  }

  public boolean remove(Object paramObject) {
    if (paramObject == null)
    { for (byte b = 0; b < this.size; b++) {
        if (this.elementData[b] == null) {
          fastRemove(b);
          return true;
        } 
      }  }
    else { for (byte b = 0; b < this.size; b++) {
        if (paramObject.equals(this.elementData[b])) {
          fastRemove(b);
          return true;
        } 
      }  }
     return false;
  }

  private void fastRemove(int paramInt) {
    this.modCount++;
    int i = this.size - paramInt - 1;
    if (i > 0) {
      System.arraycopy(this.elementData, paramInt + 1, this.elementData, paramInt, i);
    }
    this.elementData[--this.size] = null;
  }

  public int indexOf(Object paramObject) {
    if (paramObject == null)
    { for (byte b = 0; b < this.size; b++) {
        if (this.elementData[b] == null)
          return b; 
      }  }
    else { for (byte b = 0; b < this.size; b++) {
        if (paramObject.equals(this.elementData[b]))
          return b; 
      }  }
     return -1;
  }
```

以上是ArrayList类里对数组列表的操作定义。很明显，对数据的查找，增加，设置，移除都是操作的原始的Object[]，也就是对象数组的数据结构。十分直接明了。

##### LinkedList

```
  ......
  int size = 0;

  Node<E> first;

  Node<E> last;

  private static class Node<E> extends Object {
    E item;
    Node<E> next;
    Node<E> prev;
    
    Node(Node<E> param1Node1, E param1E, Node<E> param1Node2) {
      this.item = param1E;
      this.next = param1Node2;
      this.prev = param1Node1;
    }
  }

  public E get(int paramInt) {
    checkElementIndex(paramInt);
    return (E)(node(paramInt)).item;
  }

  Node<E> node(int paramInt) {
    if (paramInt < this.size >> 1) {
      Node node1 = this.first;
      for (byte b = 0; b < paramInt; b++)
        node1 = node1.next; 
      return node1;
    } 
    Node node = this.last;
    for (int i = this.size - 1; i > paramInt; i--)
      node = node.prev; 
    return node;
  }

  public E getFirst() {
    Node node = this.first;
    if (node == null)
      throw new NoSuchElementException(); 
    return (E)node.item;
  }

  public E peek() {
    Node node = this.first;
    return (E)((node == null) ? null : node.item);
  }

  public E peekFirst() {
    Node node = this.first;
    return (E)((node == null) ? null : node.item);
  }

  public E poll() {
    Node node = this.first;
    return (E)((node == null) ? null : unlinkFirst(node));
  }

  public E pollFirst() {
    Node node = this.first;
    return (E)((node == null) ? null : unlinkFirst(node));
  }

  private E unlinkFirst(Node<E> paramNode) {
    Object object = paramNode.item;
    Node node = paramNode.next;
    paramNode.item = null;
    paramNode.next = null;
    this.first = node;
    if (node == null) {
      this.last = null;
    } else {
      node.prev = null;
    }  this.size--;
    this.modCount++;
    return (E)object;
  }

  public E remove() { return (E)removeFirst(); }

  public E removeFirst() {
    Node node = this.first;
    if (node == null)
      throw new NoSuchElementException(); 
    return (E)unlinkFirst(node);
  }

  public int indexOf(Object paramObject) {
    byte b = 0;
    if (paramObject == null) {
      for (Node node = this.first; node != null; node = node.next) {
        if (node.item == null)
          return b; 
        b++;
      } 
    } else {
      for (Node node = this.first; node != null; node = node.next) {
        if (paramObject.equals(node.item))
          return b; 
        b++;
      } 
    } 
    return -1;
  }
```

以上是LinkedList的具体代码。有几个要点：

1. 定义了个内部类Node，以代表链表节点的数据结构；
2. 获取，设置，移除等方法都有顺序的概念，也就是说有First，Last的实现方法版本；
3. 可以看出，其中的操作不是只是对数组的操作，而是对一个Node节点的操作，而LinkedList实例只保存first，last节点，也就是最前和最后的节点；

### 总结

这次大致学习了List接口的两个具体实现类。和类的起名一致，有其数组和链表各自的特点。如果对随机访问有需求，自然利用ArrayList，而对顺序存取有需求，则自然利用LinkedList。