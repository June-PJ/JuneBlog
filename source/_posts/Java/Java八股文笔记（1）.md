---
title: Java八股文笔记（1）
date: '2023-07-03 16:21:15'
description: 面试小抄，Java八股文笔记
categories: 
  - Java
tags:
  - Java
  - 面经
---

## 1. 面向对象三大特征

### 封装

把对象的属性、方法私有化，仅对外界提供公共访问方法。提高了复用性和实用性。

### 继承

使用已存在的类作为父类，能够使用父类非私有的属性，也可以重写父类的方法进行扩展。提高了代码的复用性，也是多态的前提。

### 多态

程序中定义的引用变量在编译期间无法确定指向哪个类的实例对象，在运行期间才能确定，可以实现不修改源代码就可以改变程序运行时所绑定的实例。

多态可以分为静态和动态。静态主要指方法的重载（参数列表不同），动态则是运行时多态。

**Java实现多态有三个必要条件：继承、重写、向上转型（父类引用指向子类对象）。**

## 2. 内部类

### 优点

- 一个内部类对象可以访问创建它的外部类对象的所有内容
- 内部类不被同一包的其他类所见，具有很好的封装性
- 内部类有效实现了“多重继承”，优化了Java单继承的缺陷
- 匿名内部类可以很方便的定义回调

### 局部内部类和匿名内部类访问局部变量的时候，为什么变量必须要加上final？

```
public class Outer {
    void outMethod() {
        final int a = 10;
        class Inner {
            void innerMethod() {
                System.out.println(a);
            }
        }
    }
}
```

生命周期不同。局部变量存储在栈中，当方法执行结束后，非final的局部变量就会被销毁，但内部类对局部变量的引用依然存在，当内部类要调用局部变量时就会报错。加了final，可以确保局部内部类使用的变量与外层的局部变量区分开。

## 3. hashCode和equals

### hashcode()

hashCode()的作用时获取哈希码，又称散列码。哈希码的作用时确定该对象在哈希表中的索引位置。且hashCode()定义在Object下，所以 Java中任何类都有hashCode()方法。

哈希表存储的是键值对，能根据key键值快速检索出对应的value值。

**hashCode() 的默认行为是对堆上的对象产生独特值。如果没有重写hashCode()，则该 class 的两个对象无论如何都不会相等（即使这两个对象指向相同的数据）。**

### 以HashSet为例

当把对象插入HashSet时，HashSet会先计算对象的hashCode来判断对象加入的位置，如果有相同的值，则会调用equals()方法判断两个对象是否真的相等，如果相等，则不允许插入，如果不相等，则将该对象重新散列到其他位置。

**如果两个对象相等，则hashcode一定也是相同的 两个对象相等，对两个对象分别调用equals方法都返回true 两个对象有相同的hashcode值，它们也不一定是相等的。**

## 4. Integer

### Integer a= 127 与 Integer b = 127相等吗

对于基本数据类型，==比较的是值；对于引用数据类型，==比较的是对象的内存地址。

当Integer a = num的值在-128 - 127之间时，自动装箱时就不会new一个新的Integer对象，而是直接引用常量池中的Integer对象。

```
public static void main(String[] args) {
    Integer a = new Integer(3);
    Integer b = 3; // 将3自动装箱成Integer类型
    int c = 3;
    System.out.println(a == b); // false 两个引用没有引用同一对象
    System.out.println(a == c); // true a自动拆箱成int类型再和c比较
    System.out.println(b == c); // true

    Integer a1 = 128;
    Integer b1 = 128;
    System.out.println(a1 == b1); // false

    Integer a2 = 127;
    Integer b2 = 127;
    System.out.println(a2 == b2); // true
}
```

## 5. 集合

### 好处

- 容量自增长
- 提供了高性能的数据结构和算法，使编码更轻松，提高了程序速度和质量；
- 允许不同 API 之间的互操作，API之间可以来回传递集合；
- 可以方便地扩展或改写集合，提高代码复用性和可操作性。
- 通过使用JDK自带的集合类，可以降低代码维护和学习新API成本。

### 常用集合类

Map接口和Collection接口是所有集合框架的父接口：

- Collection接口的子接口包括：Set接口和List接口
- Map接口的实现类主要有：HashMap、TreeMap、Hashtable、 ConcurrentHashMap以及Properties等
- Set接口的实现类主要有：HashSet、TreeSet、LinkedHashSet等
- List接口的实现类主要有：ArrayList、LinkedList、Stack以及Vector等

### 怎么确保一个集合不能被修改？

可以使用 Collections. unmodifiableCollection(Collection c) 方法来创建一个 只读集合，这样改变集合的任何操作都会抛出 Java. lang. UnsupportedOperationException 异常。 示例代码如下：

```
List<String> list = new ArrayList<>();
list.add("x");
Collection<String> clist = Collections.unmodifiableCollection(list);
clist.add("y"); // 运行时此行报错
System.out.println(list.size());
```

## 6. 迭代器Iterator

Iterator 接口提供遍历任何 Collection 的接口。我们可以从一个 Collection 中使用迭代器方法来获取迭代器实例。迭代器取代了 Java 集合框架中的 Enumeration，迭代器允许调用者在迭代过程中移除元素。

```
List<String> list = new ArrayList<>();
Iterator<String> it = list.iterator();
while (it.hasNext()) {
    String obj = it.next();
    System.out.println(obj);
}
```

**Iterator 的特点是只能单向遍历，但是更加安全，因为它可以确保，在当前遍历的集合元素被更改的时候，就会抛出 ConcurrentModificationException 异常。**

### 快速失败机制（fail-fast）

是java集合的一种错误检测机制，当多个线程对集合进行结构上的改变的操作 时，有可能会产生 fail-fast机制。

当遍历时，通过比对modCount与expectedModCount的值判断集合是否被修改，源码如下：

```
protected transient int modCount = 0;   // 集合被修改次数

private class Itr implements Iterator<E> {
    int cursor;       // index of next element to return
    int lastRet = -1; // index of last element returned; -1 if no such
    int expectedModCount = modCount;    // 初始化时记录集合被修改次数

    public boolean hasNext() {
        return cursor != size;
    }

    @SuppressWarnings("unchecked")
    public E next() {
        checkForComodification();
        int i = cursor;
        if (i >= size)
            throw new NoSuchElementException();
        Object[] elementData = ArrayList.this.elementData;
        if (i >= elementData.length)
            throw new ConcurrentModificationException();
        cursor = i + 1;
        return (E) elementData[lastRet = i];
    }

    // 其他代码

    final void checkForComodification() {
        // 如果修改次数不一样，则代表遍历时集合被修改了，抛出异常
        if (modCount != expectedModCount)
            throw new ConcurrentModificationException();
    }
}
```

例如：假设存在两个线程（线程1、线程2），线程1通过Iterator在遍历集合A中的元素，在某个时候线程2修改了集合A的结构（是结构上面的修改，而不是简 单的修改集合元素的内容），那么这个时候程序就会抛出ConcurrentModificationException 异常，从而产生fail-fast机制。

原因：迭代器在遍历时直接访问集合中的内容，并且在遍历过程中使用一个modCount 变量。集合在被遍历期间如果内容发生变化，就会改变modCount的值。每当迭代器使用hashNext() / next()遍历下一个元素之前，都会检测 modCount变量是否为expectedmodCount值，是的话就返回遍历；否则抛出异常，终止遍历。

解决办法：

1. 在遍历过程中，所有涉及到改变modCount值得地方全部加上 synchronized。
2. 使用CopyOnWriteArrayList来替换ArrayList。

### 安全失败机制（fail-save）

fail-safe:这种遍历基于容器的一个克隆。因此，对容器内容的修改不影响遍历。java.util.concurrent包下的容器都是安全失败的,可以在多线程下并发使用,并发修改。常见的的使用fail-safe方式遍历的容器有ConcerrentHashMap和CopyOnWriteArrayList等。

原理：采用安全失败机制的集合容器，在遍历时不是直接在集合内容上访问的，而是先复制原有集合内容，在拷贝的集合上进行遍历。由于迭代时是对原集合的拷贝进行遍历，所以在遍历过程中对原集合所作的修改并不能被迭代器检测到，所以不会触发Concurrent Modification Exception。

CopyOnWriteArrayList在开始遍历时创建一个底层数组的快照，则在遍历时CopyOnWriteArrayList被修改后不会影响快照版本。

```
public Iterator<E> iterator() {
    return new CopyOnWriteArrayList.COWIterator<E>(getArray(), 0);
}

static final class COWIterator<E> implements ListIterator<E> {
    /**
     * Snapshot of the array
     */
    private final Object[] snapshot;
    /**
     * Index of element to be returned by subsequent call to next.
     */
    private int cursor;

    private COWIterator(Object[] elements, int initialCursor) {
        cursor = initialCursor;
        snapshot = elements;
    }

    public boolean hasNext() {
        return cursor < snapshot.length;
    }

    @SuppressWarnings("unchecked")
    public E next() {
        if (!hasNext())
            throw new NoSuchElementException();
        return (E) snapshot[cursor++];
    }

    // 其他代码
}
```

缺点：基于拷贝内容的优点是避免了Concurrent Modification Exception，但同样地，迭代器并不能访问到修改后的内容，即：迭代器遍历的是开始遍历那一刻拿到的集合拷贝，在遍历期间原集合发生的修改迭代器是不知道的。

### 如何边遍历边移除 Collection 中的元素？

边遍历边修改Collection的唯一正确方式是使用Iterator.remove()方法，如下：

```
Iterator<Integer> iterator = list.iterator();
while (iterator.hasNext()) {
    // do something
    iterator.remove();
}
```

一种常见的错误代码如下：

```
for(Integer i : list){
    list.remove(i);
}
```

运行以上错误代码会报 ConcurrentModificationException 异常。这是因为当使用 foreach或for(Integer i : list)语句时，会自动生成一个iterator 来遍历该 list，但同时该 list 正在被 Iterator.remove() 修改。Java 一般不允许一个线程在遍历 Collection 时另一个线程修改它。