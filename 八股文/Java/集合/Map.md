# Map

## Map接口有哪些实现类？如何选择

Map接口有很多实现类，其中比较常用的有HashMap、LinkedHashMap、TreeMap、ConcurrentHashMap。

* 对于不需要排序的场景

  * 优先考虑使用HashMap，因为它是性能最好的Map实现。

  * 如果需要保证线程安全，则可以使用ConcurrentHashMap。它的性能好于Hashtable

> 因为它在put时采用分段锁/CAS的加锁机制，而不是像Hashtable那样，无论是put还是get都做同步处理。

* 对于需要排序的场景

  * 如果需要按插入顺序排序则可以使用LinkedHashMap

    如果需要将key按自然顺序排列甚至是自定义顺序排列，则可以选择TreeMap

  * 如果需要保证线程安全，则可以使用Collections工具类将上述实现类包装成线程安全的Map

### HashMap和Hashtable

* HashMap线程不安全，键值可为空
* Hashtable线程安全，键值不能为空

### 如何得到一个线程安全的Map

1. 使用Collections工具类中的`synchronizedXXX()`方法，将线程不安全的Map包装成线程安全的Map
2. 使用JUC包下的Map，例如ConcurrentHashMap
3. Hashtable，但是不建议使用，性能太差

## ==HashMap==

![image-20220505154905695](C:\Users\yaosu\AppData\Roaming\Typora\typora-user-images\image-20220505154905695.png)

* 线程不安全
* 键值可为空

调用key的hashCode()方法得到其hashCode值，然后再通过Hash算法的后两步运算（高位运算和取模运算）来定位该键值对的存储位置，有时两个key会定位到相同的位置，表示发生了Hash碰撞

### JDK 7/8中HashMap的区别

为什么 JDK 1.7 是头插法，JDK 1.8 是尾插法？

JDK7中的HashMap，是基于**数组+链表**来实现的，它的底层维护一个Entry数组。它会根据计算的hashCode将对应的KV键值对存储到该数组中，一旦发生hashCode冲突，那么就会将该KV键值对放到对应的已有元素的后面， 此时便形成了一个链表式的存储结构。

实现方案有一个明显的缺点，即当Hash冲突严重时，在桶上形成的链表会变得越来越长，这样在查询时的效率就会越来越低，其时间复杂度为O(N)。

JDK8中的HashMap，是基于**数组+链表+红黑树**来实现的，它的底层维护一个Node数组。当链表的存储的数据个数大于等于8（数组长度大于64？）的时候，不再采用链表存储，而采用了红黑树存储结构。这么做主要是在查询的时间复杂度上进行优化，链表为O(N)，而红黑树一直是O(logN)，可以大大的提高查找性能。

#### 为什么使用红黑树不用B树

B/B+树多用于外存上时，B/B+也被成为一个磁盘友好的数据结构。

HashMap本来是数组+链表的形式，链表由于其查找慢的特点，所以需要被查找效率更高的树结构来替换。如果用B/B+树的话，在数据量不是很多的情况下，数据都会“挤在”一个结点里面，这个时候遍历效率就退化成了链表。

### 底层实现原理

#### 1. 确定哈希桶数组的索引位置

* 取key的`hashcode()`值
* 高位运算
* 取模运算

```java
方法一：
static final int hash(Object key) {   //jdk1.8 & jdk1.7
     int h;
     // h = key.hashCode() 为第一步 取hashCode值
     // h ^ (h >>> 16)  为第二步 高位参与运算
     return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
方法二：
static int indexFor(int h, int length) {  //jdk1.7的源码，jdk1.8没有这个方法，但是实现原理一样的
     return h & (length-1);  //第三步 取模运算
}
```

> 对于任意给定的对象，只要它的hashCode()返回值相同，那么程序调用方法一所计算得到的Hash码值总是相同的。我们首先想到的就是把hash值对数组长度取模运算，这样一来，元素的分布相对来说是比较均匀的。但是，模运算的消耗还是比较大的，在HashMap中是这样做的：调用方法二来计算该对象应该保存在table数组的哪个索引处。
>
> 这个方法非常巧妙，它通过h & (table.length -1)来得到该对象的保存位，而HashMap底层数组的长度总是2的n次方，这是HashMap在速度上的优化。当length总是2的n次方时，h& (length-1)运算等价于对length取模，也就是h%length，但是&比%具有更高的效率。
>
> 在JDK1.8的实现中，优化了高位运算的算法，通过hashCode()的高16位异或低16位实现的：(h = k.hashCode()) ^ (h >>> 16)，主要是从速度、功效、质量来考虑的，这么做可以在数组table的length比较小的时候，也能保证考虑到高低Bit都参与到Hash的计算中，同时不会有太大的开销。

#### 2. `put()`方法的详细执行

<img src="https://awps-assets.meituan.net/mit-x/blog-images-bundle-2016/d669d29c.png" alt="img" style="zoom: 33%;" />

> 描述一下Map put的过程
>
> 以HashMap为例
>
> 1. 首次扩容：
>
>    先判断数组是否为空，若数组为空则进行第一次扩容（resize）；
>
> 2. 计算索引：
>
>    通过hash算法，计算键值对在数组中的索引；
>
> 3. 插入数据：
>
>    - 如果当前位置元素为空，则直接插入数据；
>    - 如果当前位置元素非空，且key已存在，则直接覆盖其value；
>    - 如果当前位置元素非空，且key不存在，则将数据链到链表末端；
>    - 若链表长度达到8，则将链表转换成红黑树，并将数据插入树中；
>
> 4. 再次扩容
>
>    如果数组中元素个数（size）超过threshold，则再次进行扩容操作。

1. 判断键值对数组table[]是否为空或为null，否则执行resize()进行扩容
2. 根据键值key计算hash值得到插入的数组索引i，如果table[i]==null，直接新建节点添加，转向⑥，如果table[i]不为空，转向③
3. 判断table[i]的首个元素是否和key一样，如果相同直接覆盖value，否则转向④，这里的相同指的是hashCode以及equals
4. 判断table[i] 是否为treeNode，即table[] 是否是红黑树，如果是红黑树，则直接在树中插入键值对，否则转向⑤
5. 遍历table[i]，判断链表长度是否大于8，大于8的话把链表转换为红黑树，在红黑树中执行插入操作，否则进行链表的插入操作；遍历过程中若发现key已经存在直接覆盖value即可
6. 插入成功后，判断实际存在的键值对数量size是否超多了最大容量threshold，如果超过，进行扩容

```java
 1 public V put(K key, V value) {
 2     // 对key的hashCode()做hash
 3     return putVal(hash(key), key, value, false, true);
 4 }
 5 
 6 final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
 7                boolean evict) {
 8     Node<K,V>[] tab; Node<K,V> p; int n, i;
 9     // 步骤①：tab为空则创建
10     if ((tab = table) == null || (n = tab.length) == 0)
11         n = (tab = resize()).length;
12     // 步骤②：计算index，并对null做处理 
13     if ((p = tab[i = (n - 1) & hash]) == null) 
14         tab[i] = newNode(hash, key, value, null);
15     else {
16         Node<K,V> e; K k;
17         // 步骤③：节点key存在，直接覆盖value
18         if (p.hash == hash &&
19             ((k = p.key) == key || (key != null && key.equals(k))))
20             e = p;
21         // 步骤④：判断该链为红黑树
22         else if (p instanceof TreeNode)
23             e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
24         // 步骤⑤：该链为链表
25         else {
26             for (int binCount = 0; ; ++binCount) {
27                 if ((e = p.next) == null) {
28                     p.next = newNode(hash, key,value,null);
                        //链表长度大于8转换为红黑树进行处理
29                     if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st  
30                         treeifyBin(tab, hash);
31                     break;
32                 }
                    // key已经存在直接覆盖value
33                 if (e.hash == hash &&
34                     ((k = e.key) == key || (key != null && key.equals(k)))) 
35							break;
36                 p = e;
37             }
38         }
39         
40         if (e != null) { // existing mapping for key
41             V oldValue = e.value;
42             if (!onlyIfAbsent || oldValue == null)
43                 e.value = value;
44             afterNodeAccess(e);
45             return oldValue;
46         }
47     }

48     ++modCount;
49     // 步骤⑥：超过最大容量 就扩容
50     if (++size > threshold)
51         resize();
52     afterNodeInsertion(evict);
53     return null;
54 }
```

#### 3. 扩容机制

1. 数组的初始容量为16，而容量是以2的次方扩充的，一是为了提高性能使用足够大的数组，二是为了能使用位运算代替取模预算
2. 数组是否需要扩充是通过负载因子判断的，如果当前元素个数为数组容量的0.75时，就会扩充数组。这个0.75就是默认的负载因子，可由构造器传入。我们也可以设置大于1的负载因子，这样数组就不会扩充，牺牲性能，节省内存。
3. 为了解决碰撞，数组中的元素是单向链表类型。当链表长度到达一个阈值时（7或8），会将链表转换成红黑树提高性能。而当链表长度缩小到另一个阈值时（6），又会将红黑树转换回单向链表提高性能。
4. 对于第三点补充说明，检查链表长度转换成红黑树之前，还会先检测当前数组数组是否到达一个阈值（64），如果没有到达这个容量，会放弃转换，先去扩充数组。所以上面也说了链表长度的阈值是7或8，因为会有一次放弃转换的操作。

#### 4. 扩容过程



### HashMap为什么线程不安全？又该如何实现

HashMap在并发执行put操作时，可能会导致形成循环链表，从而引起死循环。

> **HashMap中循环链表是如何产生的？**
>
> 在多线程的情况下，当重新调整HashMap大小的时候，就会存在条件竞争，因为如果两个线程都发现HashMap需要重新调整大小了，它们会同时试着调整大小。在调整大小的过程中，存储在链表中的元素的次序会反过来，因为移动到新的bucket位置的时候，HashMap并不会将元素放在链表的尾部，而是放在头部，这是为了避免尾部遍历。如果条件竞争发生了，那么就会产生死循环了

1. 使用Collections工具类，将线程不安全的Map包装成线程安全的Map；
2. 使用java.util.concurrent包下的Map，如ConcurrentHashMap；

> 不建议使用Hashtable，虽然Hashtable是线程安全的，但是性能较差。



### 如何解决哈希冲突

为了解决碰撞，数组中的元素是单向链表类型。当链表长度到达一个阈值时，会将链表转换成红黑树提高性能。而当链表长度缩小到另一个阈值时，又会将红黑树转换回单向链表提高性能。



## ConcurrentHashMap

### 1. 与HashMap有什么区别？

HashMap是非线程安全的，这意味着不应该在多线程中对这些Map进行修改操作，否则会产生数据不一致的问题，甚至还会因为并发插入元素而导致链表成环，这样在查找时就会发生死循环，影响到整个应用程序。

Collections工具类可以将一个Map转换成线程安全的实现，其实也就是通过一个包装类，然后把所有功能都委托给传入的Map，而包装类是基于synchronized关键字来保证线程安全的（Hashtable也是基于synchronized关键字），底层使用的是互斥锁，性能与吞吐量比较低。

ConcurrentHashMap的实现细节远没有这么简单，因此性能也要高上许多。它没有使用一个全局锁来锁住自己，而是采用了**减少锁粒度**的方法，尽量减少因为竞争锁而导致的阻塞与冲突，而且ConcurrentHashMap的检索操作是不需要锁的。

### 2. 如何实现？

**1.7**

**1.8**

### 3. 如何分段分组？

get操作：

Segment的get操作实现非常简单和高效，先经过一次再散列，然后使用这个散列值通过散列运算定位到 Segment，再通过散列算法定位到元素。get操作的高效之处在于整个get过程都不需要加锁，除非读到空的值才会加锁重读。原因就是将使用的共享变量定义成 volatile 类型。

put操作：

当执行put操作时，会经历两个步骤：

1. 判断是否需要扩容；
2. 定位到添加元素的位置，将其放入 HashEntry 数组中。

插入过程会进行第一次 key 的 hash 来定位 Segment 的位置，如果该 Segment 还没有初始化，即通过 CAS 操作进行赋值，然后进行第二次 hash 操作，找到相应的 HashEntry 的位置，这里会利用继承过来的锁的特性，在将数据插入指定的 HashEntry 位置时（尾插法），会通过继承 ReentrantLock 的 tryLock() 方法尝试去获取锁，如果获取成功就直接插入相应的位置，如果已经有线程获取该Segment的锁，那当前线程会以自旋的方式去继续的调用 tryLock() 方法去获取锁，超过指定次数就挂起，等待唤醒。

## LinkedHashMap

### 介绍

LinkedHashMap使用**双向链表**来维护key-value对的顺序（其实只需要考虑key的顺序），迭代顺序与key-value对的插入顺序保持一致。

### 底层原理

LinkedHashMap继承于HashMap，它在HashMap的基础上，通过维护一条双向链表，解决了HashMap不能随时保持遍历顺序和插入顺序一致的问题

在实现上，LinkedHashMap很多方法直接继承自HashMap，仅为维护双向链表重写了部分方法

## TreeMap

TreeMap基于红黑树实现，根据其键的自然顺序进行排序，或者也可以自定义排序（根据构造时传入的Comparator）