---
title: java集合框架-HashSet
categories: 数据结构与算法
---

### 继承体系

```
public class HashSet<E>
  extends AbstractSet<E>
  implements Set<E>, Cloneable, Serializable
```

### 实现分析

其实，HashSet类里面维护了一个HashMap<E, Object>类型的变量。那就很明显了，对HashSet的元素操作，其实都是作为HashMap的键的操作。

```
  ......
  ......

  private HashMap<E, Object> map;
  private static final Object PRESENT = new Object();

  ......
  ......

  public boolean add(E paramE) { return (this.map.put(paramE, PRESENT) == null); }

  public boolean contains(Object paramObject) { return this.map.containsKey(paramObject); }

  public boolean isEmpty() { return this.map.isEmpty(); }

  public Iterator<E> iterator() { return this.map.keySet().iterator(); }

  public boolean remove(Object paramObject) { return (this.map.remove(paramObject) == PRESENT); }

  public int size() { return this.map.size(); }

  ......
  ......
```

如以上代码，其实HashSet的元素操作其实都委托给了HashMap类型的成员变量m。

### 总结

简单点说，HashSet就是在实现Set接口的前提下，将操作逻辑都委派给了HashMap。HashMap的具体实现请看我以前的分析。