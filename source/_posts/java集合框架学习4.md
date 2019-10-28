---
title: java集合框架学习4-以Abstract开头的抽象类
categories: java
---

### 前言

在之前，我讨论了几个较为基础的接口，这几个接口也是我们日常比较常见到的，用到的；也是数据结构这门课强调最为多的几个接口。

而这次这几个接口会成为抽象类，也就是说直接实现了我前面讨论了的接口的抽象类。

接下来就直接进入正题了。

### 被实现为Abstract抽象类的基础数据结构

* Collection
* List
* Map
* Queue
* SequentialList
* Set

从以上的数据结构类型可以看出，在我之前讨论过的基础数据结构的接口来说，除了接口Dequq，抽象类Dictionary，具体类Stack之外，接口定义都实现为了抽象类。而且还加了个SequentialList的抽象类。

而在上面显示的数据结构，大门的继承接口和相互之间的继承关系又如下：

AbstractCollection

```
public abstract class AbstractCollection<E>
  extends Object
  implements Collection<E>
```

AbstractList

```
public abstract class AbstractList<E>
  extends AbstractCollection<E>
  implements List<E>
```

AbstractMap

```
public abstract class AbstractMap<K, V>
  extends Object
  implements Map<K, V>
```


AbstractQueue

```
public abstract class AbstractQueue<E>
  extends AbstractCollection<E>
  implements Queue<E>
```

AbstractSequentialList

```
public abstract class AbstractSequentialList<E>
  extends AbstractList<E>
```

AbstractSet

```
public abstract class AbstractSet<E>
  extends AbstractCollection<E>
  implements Set<E>
```

从以上列出的定义可以看出，除了AbstractMap，其他抽象类都继承自AhstractCollection。所以都具有AbstractCollection的实现。

### AbstractCollection

此个抽象类继承自Object对象，而且除了iterator方法和size方法定义为未实现的抽象方法，都实现了其他类接口Collection的全部接口方法。方法的定义都放在了接口中了，所以此时具体的类定义，如果没有具体实现的，便定义为抽象方法，如果有具体定义的，便定义为非抽象方法。

值得注意（对面向对象的类方法定义和实现和继承是有借鉴意义的）：

1. 新定义了接口中没有的方法，覆盖了继承的Object类里面的toString方法；
2. 定义了个内部私有实现方法来供toArray公有方法使用，如下：

```
  private static <T> T[] finishToArray(T[] paramArrayOfT, Iterator<?> paramIterator)
```

对于具体的实现的接口方法是什么，请看之前的接口分析。而方法的具体实现内部逻辑，并不是我今天讨论的重点。

### AbstractList，AbstractQueue，AbstractSet

这几个抽象类对应的接口分别是我之前已经讨论过的List，Queue，Set接口。

而且这几个抽象类都有一个共同点，就是继承自AbstractCollection抽象类。也就是说，作者在实现各自接口的抽象类之前，除了将实现接口当成是一个任务，而且在设计上，将其共性都放置到AbstractCollection抽象类中。

##### AbstractList

这个类的实现基本操作有和List接口定义的大致一样，需要请看之前的List接口博文。

重点有几个：

1. 对于没定义为抽象方法，但在接口中已经申明定义的方法，具体定义都以抛出一个异常作为实现。

```
public E xxx(xxx xxx) { throw new UnsupportedOperationException(); }
```

2. 定义有一个removeRange方法，访问形式申明为protected，并让共有方法clear调用，我的猜想是：想让clear的具体实现封装在类继承体系内，也就是说，子类可以通过继承removeRange重新定义，实现对clear方法实现的更改。

```
  public void clear() { removeRange(0, size()); }
  protected void removeRange(int paramInt1, int paramInt2) {
      .......
  }
```

3. 如果定义为私有方法的，必定是被公有方法调用，而不曝露给调用者。如下：

```
  private void rangeCheckForAdd(int paramInt) {
    if (paramInt < 0 || paramInt > size()) {
      throw new IndexOutOfBoundsException(outOfBoundsMsg(paramInt));
    }
  }
  
  private String outOfBoundsMsg(int paramInt) { return "Index: " + paramInt + ", Size: " + size(); }

  public ListIterator<E> listIterator(int paramInt) {
    rangeCheckForAdd(paramInt);
    
    return new ListItr(paramInt);
  }
```

4. 令人诧异的是，一个抽象类的内部竟然还有一个私有类的定义：

```
  private class Itr
    extends Object
    implements Iterator<E> {
      ......
    }

  private class ListItr extends Itr implements ListIterator<E> {
    .....
  }
```

可以看出，应该是一个Iterator和ListIterator接口的自身内部实现，也就是说，自己内部定义了接口的类实现，而且只供此类内部的方法逻辑使用。

当然，内部类也不是我今天想讨论的重点。


##### AbstractQueue

这个类的定义还是比较清楚的。

```
  public boolean add(E paramE) { ... }

  public E remove() { ... }

  public E element() { ... }

  public void clear() { ... }

  public boolean addAll(Collection<? extends E> paramCollection) { ... }
```

但是有很疑惑的一点，便是Queue的接口定义：

```
public interface Queue<E> extends Collection<E> {
  boolean add(E paramE);
  
  boolean offer(E paramE);
  
  E remove();
  
  E poll();
  
  E element();
  
  E peek();
}

```

那么offer，poll和peek方法的定义哪里去了？实现接口，却没实现相应的接口方法，而且居然还没报错？

想了一想，还是暂且放一边吧。但是有一点，就是继承自AbstractQueue的类定义是有定义以上抽象类没有定义的接口的。这个问题也暂且当成是个疑问吧。

##### AbstractSet

和AbstractQueue一样，这个抽象类的定义也比较简单，只有三个方法的定义：

```
  public boolean equals(Object paramObject) { ... }

  public int hashCode() { ... }

  public boolean removeAll(Collection<?> paramCollection) { ... }
```

而且我们又发现问题了。便是Set接口里面的一大堆方法几乎都没有实现的定义，连申明都没有。。。和上面的问题其实是一样的，也是我暂且我想得知的。


### AbstractSequentialList

这个类从以上申明可以知道，是继承自AbstractList抽象类的，其内部方法定义实现如下：

```
  public E get(int paramInt) { ... }

  public E set(int paramInt, E paramE) { ... }

  public void add(int paramInt, E paramE) { ... }

  public E remove(int paramInt) { ... }

  public boolean addAll(int paramInt, Collection<? extends E> paramCollection) { ... }

  public Iterator<E> iterator() { return listIterator(); }
  
  public abstract ListIterator<E> listIterator(int paramInt);
```

可以看出，有一些，如get，set，add，remove是对AbstractList实现的完善，一些是自己对AbstractList实现的方法的重写，如addAll。或者是有一些方法是对子类方法的适配，如iterator。

### AbstractMap

这个抽象类是实现Map接口，而且是Object的直接子类。

这个抽象子类的实现功能大体和Map接口定义的方法一样，但有几个除了公有接口方法的实现之外，还有几个发现：

1. 私有的静态方法实现：

```
  private static boolean eq(Object paramObject1, Object paramObject2) { return (paramObject1 == null) ? ((paramObject2 == null)) : paramObject1.equals(paramObject2); }
```

2. 未实现的接口方法定义：

```
  public abstract Set<Map.Entry<K, V>> entrySet();
```

3. 公有内部静态类定义：

```
  public static class SimpleEntry<K, V>
    extends Object
    implements Map.Entry<K, V>, Serializable { ... }

  public static class SimpleImmutableEntry<K, V>
    extends Object
    implements Map.Entry<K, V>, Serializable { ... }
```

实现的是Map接口内的公有静态接口：

```
  public static interface Entry<K, V> { ... }
```

大概的意思和作用应该是，Map代表键值列表的操作，而entry代表单个键值对的操作。具体的内部思想和实现今天不讨论。

### 总结

通过以上的大概的了解，得知几个重要接口的抽象类实现的大致。

作用有几个：

1. 清楚了抽象类的继承关系；
2. 清楚了抽象类对接口的实现大概脉络；
3. 了解了面向对类库抽象类设计的一些特性，比如访问性，内部接口，内部类，继承机制下方法的重写等；
4. 发现了一些以前不知道的问题：接口定义的方法可以不在一级继承接口的方法内实现？

问题需要以后去解决，今天先到这。