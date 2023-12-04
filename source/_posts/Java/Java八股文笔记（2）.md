---
title: Java八股文笔记（2）
date: '2023-07-05 11:37:53'
description: 面试小抄，Java八股文笔记
---

## 1. ArrayList

### ArrayList优缺点

优点：

- ArrayList 底层以数组实现，是一种随机访问模式。ArrayList 实现了 RandomAccess 接口，因此查找的时候非常快。
- ArrayList 在顺序添加一个元素的时候非常方便。

缺点：

- 删除元素的时候，需要做一次元素复制操作。如果要复制的元素很多，那么就会比较耗费性能。
- 插入元素的时候，也需要做一次元素复制操作，缺点同上。

**ArrayList 比较适合顺序添加、随机访问的场景。**

### ArrayList扩容机制

当采用无参构造时，会分配一个空数组：*DEFAULTCAPACITY_EMPTY_ELEMENTDATA*

```
private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};

transient Object[] elementData; // non-private to simplify nested class access

public ArrayList() {
    this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
}
```

当采用有参构造时，如果传入的是合法int值，会直接创建该值大小的数组

```
private static final Object[] EMPTY_ELEMENTDATA = {};

transient Object[] elementData; // non-private to simplify nested class access

public ArrayList(int initialCapacity) {
    if (initialCapacity > 0) {
        this.elementData = new Object[initialCapacity];
    } else if (initialCapacity == 0) {
        this.elementData = EMPTY_ELEMENTDATA;
    } else {
        throw new IllegalArgumentException("Illegal Capacity: "+
                initialCapacity);
    }
}
```

当采用有参构造时，如果传入一个集合，如果集合长度不为零，会根据集合的类型决定是否创建新数组

```
private static final Object[] EMPTY_ELEMENTDATA = {};

transient Object[] elementData; // non-private to simplify nested class access

public ArrayList(Collection<? extends E> c) {
    Object[] a = c.toArray();
    if ((size = a.length) != 0) {
        if (c.getClass() == ArrayList.class) {
            elementData = a;
        } else {
            elementData = Arrays.copyOf(a, size, Object[].class);
        }
    } else {
        // replace with empty array.
        elementData = EMPTY_ELEMENTDATA;
    }
}
```

### add()和addAll()扩容机制

**add()**：如果ArrayList底层数组长度为零，则会扩容至10；如果数组长度超过10，则每次扩容至原长的1.5倍（原本长度向右移1位后加原长，例如15 >> 1 = 7，7 + 15 = 22）

**addAll()**：将添加的集合与底层数组的长度进行比较，取较大值为扩容数组后的长度

### ArrayList和LinkedList的区别

**ArrayList：**

1. 基于数组，需要连续内存
2. 随机访问快（根据下标访问）
3. 尾部插入、删除性能可以，其他部分插入、删除都会移动数据，因此性能会低
4. 可以利用CPU缓存（缓存局部性原理）

**LinkList：**

1. 基于双向链表，无需连续内存
2. 随机访问慢（要沿着链表遍历）
3. 头尾插入、删除性能搞；中间插入、删除需要遍历到该位置，所以性能比ArrayList慢
4. 占用内存多

### ArrayList 和 Vector 的区别

这两个类都实现了 List 接口（List 接口继承了 Collection 接口），他们都是有序集合。

- Vector 使用了 Synchronized 来实现线程同步，是线程安全的，而ArrayList 是非线程安全的。
- 性能：ArrayList 在性能方面要优于 Vector。
- 扩容：ArrayList 和 Vector 都会根据实际的需要动态的调整容量，只不过在Vector 扩容每次会增加 1 倍，而 ArrayList 只会增加 50%。
- Vector类的所有方法都是同步的。可以由两个线程安全地访问一个Vector对象、但是一个线程访问Vector的话代码要在同步操作上耗费大量的时间。
- Arraylist不是同步的，所以在不需要保证线程安全时时建议使用Arraylist。

## 2. Set接口

### HashSet 的实现原理

HashSet 是基于 HashMap 实现的，HashSet的值存放于HashMap的key上，HashMap的value统一为PRESENT，因此 HashSet 的实现比较简单，相关 HashSet 的操作，基本上都是直接调用底层HashMap 的相关方法来完成，HashSet 不允许重复的值。下面是HashSet的部分源码：

```
private transient HashMap<E, Object> map;
private static final Object PRESENT = new Object();

public HashSet() {
    map = new HashMap<>();
}

public boolean add(E e) {
    return map.put(e, PRESENT) == null;
}

public boolean remove(Object o) {
    return map.remove(o) == PRESENT;
}
```

### HashSet与HashMap的区别

| **HashSet**                                                  | **HashMap**                                              |
| ------------------------------------------------------------ | -------------------------------------------------------- |
| 实现了Set接口                                                | 实现了Map接口                                            |
| 仅存储对象                                                   | 存储键值对                                               |
| 调用 add（） 方法向Set 中添加元素                            | 调用 put（）向 map中添加元素                             |
| HashSet 使用成员对象来计 算 hashcode 值，对于两个对象来说hashcode 可能相同，所以 equals()方法用来判断对象的相等性，如果两个对象不同的话，那么返回 false | HashMap 使用键（Key）计算 Hashcode                       |
| HashSet 较 HashMap 来说比较慢                                | HashMap 相对于HashSet 较快，因为它是使用唯一的键获取对象 |

## 3. Map接口

### HashMap的实现原理

HashMap是基于哈希表的Map接口的非同步实现。此实现提供所有可选的映射操作，并允许使用null值和null键。此类不保证映射的顺序，特别是它不保证该顺序恒久不变。

HashMap实际上是一个“链表散列”的数据结构，即数组和链表的结合体。

HashMap 基于 Hash 算法实现的

- 当我们往Hashmap中put元素时，利用key的hashCode计算出当前对象的元素在数组中的下标
- 存储时，如果出现hash值相同的key，此时有两种情况。(1)如果key相同，则覆盖原始值；(2)如果key不同（出现冲突），则将当前的key-value 放入链表中
- 获取时，直接找到hash值对应的下标，在进一步判断key是否相同，从而找到对应值。
- 理解了以上过程就不难明白HashMap是如何解决hash冲突的问题，核心就是使用了数组的存储方式，然后将冲突的key的对象放入链表中，一旦发现冲突就在链表中做进一步的对比。

**Jdk 1.8中对HashMap的实现做了优化，当链表中的节点数据超过八个之后，该链表会转为红黑树来提高查询效率，从原来的O(n)到O(logn)**

### HashMap的put方法具体流程

当我们put的时候，首先计算 key的hash值，这里调用了 hash方法，hash方法实际是让key.hashCode()与key.hashCode()>>>16进行异或操作，高16bit补0，一个数和0异或不变，所以 hash 函数大概的作用就是：高16bit不变，低16bit和高16bit做了一个异或，目的是减少碰撞。

```
/**
 * Associates the specified value with the specified key in this map.
 * If the map previously contained a mapping for the key, the old
 * value is replaced.
 *
 * @param key key with which the specified value is to be associated
 * @param value value to be associated with the specified key
 * @return the previous value associated with <tt>key</tt>, or
 *         <tt>null</tt> if there was no mapping for <tt>key</tt>.
 *         (A <tt>null</tt> return can also indicate that the map
 *         previously associated <tt>null</tt> with <tt>key</tt>.)
 */
public V put(K key, V value) {
    return putVal(hash(key), key, value, false, true);
}

static final int hash(Object key) {
    int h;
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}

/**
 * Implements Map.put and related methods.
 *
 * @param hash hash for key
 * @param key the key
 * @param value the value to put
 * @param onlyIfAbsent if true, don't change existing value
 * @param evict if false, the table is in creation mode.
 * @return previous value, or null if none
 */
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
               boolean evict) {
    //...
}
```

### HashMap是怎么解决哈希冲突的？

Hash，一般翻译为“散列”，也有直接音译为“哈希”的，这就是把任意长度的输入通过散列算法，变换成固定长度的输出，该输出就是散列值（哈希值）；这种转换是一种压缩映射，也就是，散列值的空间通常远小于输入的空间，不同的输入可能会散列成相同的输出，所以不可能从散列值来唯一的确定输入值。简单的说就是一种将任意长度的消息压缩到某一固定长度的消息摘要的函数。

**所有散列函数都有如下一个基本特性：根据同一散列函数计算出的散列值如果不同，那么输入值肯定也不同。但是，根据同一散列函数计算出的散列值如果相同，输入值不一定相同。**

哈希冲突：当两个不同的输入值，根据同一散列函数计算出相同的散列值的现象，我们就把它叫做碰撞（哈希碰撞）。

#### 如何解决哈希冲突

1. 散列表

即使用散列表，结合数组与链表，将hash值相同的数据通过链表连接

2. 二次扰动函数

即hash函数。HashMap初始的容量大小DEFAULT_INITIAL_CAPACITY = 1 << 4（即 2的四次方16）要远小于int类型的范围，所以我们如果只是单纯的用hashCode取余来获取对应的bucket这将会大大增加哈希碰撞的概率，并且最坏情况下还会将HashMap变成一个单链表，所以我们还需要对hashCode作一定的优化 hash()函数。

上面提到的问题，主要是因为如果使用hashCode取余，那么相当于参与运算的只有hashCode的低位，高位是没有起到任何作用的，所以我们的思路就是让 hashCode取值出的高位也参与运算，进一步降低hash碰撞的概率，使得数据分布更平均，我们把这样的操作称为扰动，在JDK 1.8中的hash()函数如下：

```
static final int hash(Object key) {
    int h;
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);  //// 与自己右移16位
进行异或运算（高低位异或）
}
```

**这比在JDK 1.7中，更为简洁，相比在1.7中的4次位运算，5次异或运算（9次扰动）；在1.8中，只进行了1次位运算和1次异或运算（2次扰动）；**

3. 红黑树

通过上面的链地址法（使用散列表）和扰动函数我们成功让我们的数据分布更平均，哈希碰撞减少，但是当我们的HashMap中存在大量数据时，加入我们某个 bucket下对应的链表有n个元素，那么遍历时间复杂度就为O(n)，为了针对这个问题，JDK1.8在HashMap中新增了红黑树的数据结构，进一步使得遍历复杂度降低至O(logn)；

### HashMap 与 HashTable 的区别

1. 线程安全： HashMap 是非线程安全的，HashTable 是线程安全的；HashTable 内部的方法基本都经过 synchronized 修饰。（如果你要保证线程安全的话就使用ConcurrentHashMap 吧！）；
2. 效率： 因为线程安全的问题，HashMap 要比 HashTable 效率高一点。另外，HashTable 基本被淘汰，不要在代码中使用它；
3. 对Null key 和Null value的支持： HashMap 中，null 可以作为键，这样的键只有一个，可以有一个或多个键所对应的值为 null。但是在HashTable 中 put 进的键值只要有一个 null，直接抛
   NullPointerException。
4. 初始容量大小和每次扩充容量大小的不同 ： ①创建时如果不指定容量初始值，Hashtable 默认的初始大小为11，之后每次扩充，容量变为原来的2n+1。HashMap 默认的初始化大小为16。之后每次扩充，容量变为原来的2倍。②创建时如果给定了容量初始值，那么 Hashtable 会直接使用你给定的大小，而 HashMap 会将其扩充为2的幂次方大小。
5. 底层数据结构： JDK1.8 以后的 HashMap 在解决哈希冲突时有了较大的变化，当链表长度大于阈值（默认为8）时，将链表转化为红黑树，以减少搜索时间。Hashtable 没有这样的机制。
6. 推荐使用：在 Hashtable 的类注释可以看到，Hashtable 是保留类不建议使用，推荐在单线程环境下使用 HashMap 替代，如果需要多线程使用则用 ConcurrentHashMap 替代。