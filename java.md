# 目录|Content
<a href='#database'>一、数据库</a>  
　　<a href='#datasource'>1.数据库链接池</a>  
<a href='#javaContainer'>二、java集合</a>  
　　<a href='#HashMap'>1.HashMap</a>  
　　　　<a href='#HashMapPutMethod'>1.1 put方法</a>  
　　　　<a href='#HashMap2Pow'>1.2 HashMap的长度为什么要是2的n次方</a>  
　　<a href='#ArrayList'>2.ArrayList</a>  
<a href='#javaConcurrent'>三、java并发</a>  
　　<a href='#concurrentContainer'>1.并发容器</a>  
　　　　<a href='#CopyOnWriteList'>1.1 CopyOnWriteList</a>  
　　　　<a href='#ConcurrentHashMap'>1.2 ConcurrentHashMap</a>  
　　　　　　<a href='#mapDifference'>1.2.1 ConcurrentHashMap和HashMap的区别</a>  
　　　　　　<a href='#ConcurrentHashMapPutMethod'>1.2.2 put方法</a>  
　　　　<a href='#blockingQueue'>1.n BlockingQueue</a>  
　　<a href='#concurrentTools'>2.同步组件</a>  
　　　　<a href='#AQSIntr'>2.1 AQS介绍(AbstractQueuedSynchronizer)</a>  
　　　　<a href='#ReentrantLock'>2.2 ReentrantLock</a>  
　　　　<a href='#ReentrantReadWriteLock'>2.3 ReentrantReadWriteLock</a>  
　　　　<a href='#CountDownLatch'>2.4 CountDownLatch</a>  
　　　　<a href='#Semaphore'>2.5 Semaphore</a>  
　　　　<a href='#CyclicBarrier'>2.6 CyclicBarrier</a>  
　　<a href='#threadPool'>3.线程池</a>  
　　　　<a href='#threadPoolAdv'>3.1 线程池的优点</a>  
　　　　<a href='#ThreadPoolExecutor'>3.2 ThreadPoolExecutor</a>  
　　　　　　<a href='#corePoolSizeRela'>3.2.1 corePoolSize、maximumPoolSize和workQueue间的关系</a>  
　　　　　　<a href='#workQueue'>3.2.2 workQueue的三种处理方式</a>  
　　　　　　<a href='#threadPoolConf'>3.2.3 线程池的合理配置</a>  
　　　　<a href='#threadPoolStatus'>3.3 线程池的状态</a>  
　　　　<a href='#threadPoolMethod'>3.4 ThreadPoolExecutor提供的方法</a>  
　　　　<a href='#4threadPool'>3.5 Executor提供的四种线程池</a>  
　　<a href='#Concurrentexpand'>4.并发拓展</a>  
　　　　<a href='#SpringAdnThreadSafe'>4.1 Spring与线程安全</a>  
　　　　<a href='#volatile'>4.2 volatile关键字</a>  
<a href='#javaIO'>四、BIO、NIO、AIO</a>  
　　<a href='#BIO'>1.BIO</a>  
　　<a href='#fakeSynIo'>2.伪异步I/O</a>  
　　<a href='#NIO'>3.NIO</a>  
　　　　<a href='#Buffer'>3.1 缓冲区Buffer</a>  
　　　　<a href='#Channel'>3.2 通道Channel</a>  
　　　　<a href='#Selector'>3.3 多路复用器Selector</a>  
　　　　<a href='#IOTimePic'>3.4 服务端和客户端的通讯时序图</a>
　　　　<a href='#NIOAdvantage'>3.5 NIO的优点</a>
　　<a href='#AIO'>4.AIO</a>  


<a href='#reference'>reference</a>  

----



<a id='database'></a>
# 一、数据库
<a id='datasource'></a>
## 1. 数据库连接池
[详细了解请参照DBCP官方文档](http://commons.apache.org/proper/commons-dbcp/configuration.html)  
参数相关：

- username：数据库的用户名。
- password：数据库的密码。
- url：数据库url。
- driverClassName：数据库的驱动。

- initialSize：初始化连接数目。
- maxActive：最大活动连接。连接池在同一时间能够分配的最大活动连接的数量，如果设置为非正数则表示不限制。
- maxIdle：最大空闲连接。连接池中容许保持空闲状态的最大连接数量，超过的空闲连接将被释放，如果设置为负数表示不限制。
- minIdle：最小空闲连接。连接池中容许保持空闲状态的最小连接数量，低于这个数量将创建新的连接，如果设置为0则不创建。
- maxWait：连接池中连接用完时，新的请求等待时间（毫秒）。如果设置为-1表示无限等待，超过时间则抛出异常

- removeAbandoned：是否清除已经超过“removeAbandonedTimout”设置的无效连接。如果值为“true”则超过“removeAbandonedTimout”设置的无效连接将会被清除。
- removeAbandonedTimeout：活动连接（未被close的活动连接）的最大空闲时间。单位为秒，超过此时间的连接会被释放到连接池中。


<a id='javaContainer'></a>
# 二、java集合
![collection](https://github.com/TimePickerWang/ContradictoryBattle/blob/master/images/collection.jpg?raw=true)


<a id='HashMap'></a>
## 1.HashMap
&emsp;&emsp;HashMap底层即为下面 Node<K,V>类型的数组，每个 Node<K,V>包含四个字段：通过key计算出的hash，key值，value值，和指向下一个节点的指针next，因此Node<K,V>也可以连为一个链表。HashMap允许存储为null的键和值。

```java
static class Node<K,V> implements Map.Entry<K,V> {
    final int hash;
    final K key;
    V value;
    Node<K,V> next;

    Node(int hash, K key, V value, Node<K,V> next) {
        this.hash = hash;
        this.key = key;
        this.value = value;
        this.next = next;
    }
    ...
    ...
```

<a id='HashMapPutMethod'></a>
### 1.1 put方法 
```java
static final float DEFAULT_LOAD_FACTOR = 0.75f;  // 加载因子
static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // 初始容量（即table长度）
static final int TREEIFY_THRESHOLD = 8;  // 某个桶由链表转为树的阈值
static final int UNTREEIFY_THRESHOLD = 6;  // 某个桶由树转回链表的阈值
static final int MIN_TREEIFY_CAPACITY = 64; // 某个桶转为树时的table的最小容量

static final int hash(Object key) {
    int h;
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}

public V put(K key, V value) {
    return putVal(hash(key), key, value, false, true);
}

final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
               boolean evict) {
    Node<K,V>[] tab; Node<K,V> p; int n, i;
    if ((tab = table) == null || (n = tab.length) == 0)
        n = (tab = resize()).length;
    if ((p = tab[i = (n - 1) & hash]) == null)
        tab[i] = newNode(hash, key, value, null);
    else {
        Node<K,V> e; K k;
        if (p.hash == hash &&
            ((k = p.key) == key || (key != null && key.equals(k))))
            e = p;
        else if (p instanceof TreeNode)
            e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
        else {
            for (int binCount = 0; ; ++binCount) {
                if ((e = p.next) == null) {
                    p.next = newNode(hash, key, value, null);
                    if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                        treeifyBin(tab, hash);
                    break;
                }
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    break;
                p = e;
            }
        }
        if (e != null) { // existing mapping for key
            V oldValue = e.value;
            if (!onlyIfAbsent || oldValue == null)
                e.value = value;
            afterNodeAccess(e);
            return oldValue;
        }
    }
    ++modCount;
    if (++size > threshold)
        resize();
    afterNodeInsertion(evict);
    return null;
}
```
**注：代码中的两个常量DEFAULT_INITIAL_CAPACITY和MIN_TREEIFY_CAPACITY都是指的table的长度(Node<K,V>数组的长度)，而不是table中Node<K,V>的数量，而扩容是判断的是size和threshold的大小，此时size指的是Node<K,V>的数量**

（1）调用put方法时首先要通过key字段计算hash码（hash码为int类型），然后将计算出的hash码和该hash码右移16位后的值做'^'运算（混合原始哈希码的高位和低位，以此来加大低位的随机性，可参考[JDK 源码中 HashMap 的 hash 方法原理是什么？](https://www.zhihu.com/question/20733617/answer/111577937)），所的的结果赋给Node<K,V>对象hash字段。注意，当key字段是null，赋给Node<K,V>对象hash字段值为0，这也表明当存储的key为null时，该Node<K,V>对象始终在table第0个桶上。  
（2）将（1）中计算出的hash值和哈希表table（即Node<K,V>数组 ）的长度(n-1)做'&'运算来确定新对象在table中的位置i。如果table为空或者table的长度为0需调用resize()来进行扩容。  
（3）如果位置i上为null，直接创建新的Node<K,V>对象放在table中的i位置上。  
（4）如果位置i上不为null，进行如下操作：   
　　（4.1）比较该位置原Node对象的hash值与key值和新的hash值、key值是否相等，相等则把旧Node进行替换。  
　　（4.2）如果该位置的Node对象和新的key值或者hash值不相等，判断该位置的Node对象是否TreeNode类型，是的话在树中找到和hash值与key相等的节点，找到则替换，没找到则插入新的TreeNode到树中。  
　　（4.3）如果该位置的Node对象和新的key值或者hash值不相等，且该位置的Node对象也不是TreeNode类型，则遍历该位置的Node对象组成的链表。如果在链表中找到了相等的Node对象，把旧Node进行替换。如果没找到，将新的对象插入到链表的尾部，插入后要判断两个条件：**该链表的长度是否大于8（TREEIFY_THRESHOLD），table的长度是否大于或等于64（MIN_TREEIFY_CAPACITY）**，如果两个条件都满足，则将该链表转为树；如果**该链表的长度大于8，table的长度小于64**，则调用resize()方法。  
（5）如果table中找到了和新的hash值与key相等的Node，进行替换后需要返回原Node的value值；如果没有找到相应的Node，在新增节点后需要 ++modCount（table的修改次数），++size操作（当前table的Node个数），并判断size和threshold（需进行扩容的阈值，初始为DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY，即12）的大小，当size>threshold时，需要调用resize()进行扩容。**扩容就是创建一个新的table，其长度和threshold阈值都增长为原来的2倍，然后遍历原table，将其中的每一个Node分配到新table对应的位置上（这就是rehash，在多线程的情况下rehash可能出现死循环，这里不做讨论）。**


<a id='HashMap2Pow'></a>
### 1.2 HashMap的长度为什么要是2的n次方
```java
tab[i = (n - 1) & hash]
```
1.效果上等同于取模运算，但效率比取模高  
　　在通过上式计算元素在table中的位置时，算法实际就是取模，即hash%length（table的长度），但计算机中直接取模效率不如位运算，因此源码中优化成了hash&(length-1)，而hash%length==hash&(length-1)的前提是length是2的n次方。  
2.减少碰撞  
　　由于length是2的n次方，hash&(length-1)时候，每一位都能  &1（2的次幂减1后二进制末尾都是1），也就是和……1111111进行与运算，只要hash足够乱，产生碰撞的可能性就越小。


<a id='ArrayList'></a>
## 2.ArrayList
ArrayList底层为Object数组， 先看看它的几个常量和构造函数：

```java
// ArrayList默认的初始容量为10
private static final int DEFAULT_CAPACITY = 10;
// 调用有参且初始值为0的构造方法赋为空的数组
private static final Object[] EMPTY_ELEMENTDATA = {};
// 调用无参的构造方法赋为空的数组
private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};
// ArrayList的最大容量
private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;

// 有参构造函数
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

// 无参构造函数
public ArrayList() {
    this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
}
```
**注意**：调用new ArrayList()和new ArrayList(0)会赋值为两个不同的空数组，具体原因看看如下代码：

```java
public boolean add(E e) {
    ensureCapacityInternal(size + 1);  // Increments modCount!!
    elementData[size++] = e;
    return true;
}

private void ensureCapacityInternal(int minCapacity) {
    if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
        minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
    }
    ensureExplicitCapacity(minCapacity);
}

private void ensureExplicitCapacity(int minCapacity) {
    modCount++;

    if (minCapacity - elementData.length > 0)
        grow(minCapacity);
}

private void grow(int minCapacity) {
    // overflow-conscious code
    int oldCapacity = elementData.length;
    // List的长度变长为原List长度的1.5倍
    int newCapacity = oldCapacity + (oldCapacity >> 1);
    if (newCapacity - minCapacity < 0)
        newCapacity = minCapacity;
    if (newCapacity - MAX_ARRAY_SIZE > 0)
        newCapacity = hugeCapacity(minCapacity);
    elementData = Arrays.copyOf(elementData, newCapacity);
}

private static int hugeCapacity(int minCapacity) {
    if (minCapacity < 0) // overflow
        throw new OutOfMemoryError();
    return (minCapacity > MAX_ARRAY_SIZE) ?
        Integer.MAX_VALUE :
        MAX_ARRAY_SIZE;
}
```
&emsp;&emsp;以上代码可以看到：在它们在添加第一个元素后会出现两种完全不同的情况：如果是调用无参的构造方法，添加第一个元素后List会自动增长到10的长度。如果是调用new ArrayList(0)，添加第一个元素后List的长度是1。  
　　**扩容规则：**  
　　从上面也可以看出：若原List的长度为n，则添加一个元素后会计算一下新List可能的长度为**1.5n（取整）**  
　　1.此时如果1.5n<(n+1)，则新List的长度在原List的长度上加1，即（n+1）  
　　2.如果1.5n>List的最大容量MAX_ARRAY_SIZE，会再判断（n+1）和MAX_ARRAY_SIZE的大小，如果（n+1）大，新List的长度增长为Integer.MAX_VALUE，否则为MAX_ARRAY_SIZE。  
　　3.如果 (n+1) < 1.5n < MAX_ARRAY_SIZE，那么新Lis长度为1.5n。  
　　4.把原List复制到扩容后的List中，把新元素添加进去。  
　　**ArrayList移除元素**：ArrayList在移除元素时也会新建一个组数，然后将原数组的对象复制到新数组中。


<a id='javaConcurrent'></a>
# 三、java并发
**J.U.C包的构成**
![J.U.C](https://github.com/TimePickerWang/ContradictoryBattle/blob/master/images/juc.jpg?raw=true)


<a id='concurrentContainer'></a>
## 1.并发容器
```java
ArrayList   ->     CopyOnWriteList
HashSet     ->     CopyOnWriteArraySet
TreeSet     ->     ConcurrentSkipListSet（不允许null元素）
HashMap     ->     ConcurrentHashMap
TreeMap     ->     ConcurrentSkipListMap
```

<a id='CopyOnWriteList'></a>
### 1.1 CopyOnWriteList
所有的读操作都是在原list上读的，不加锁。所有的写操作需要加锁



缺点：  
1.对list做写操作的时候需要做拷贝，很消耗内存，当元素较多时可能会造成YoungGC或者FullGC。  
2.不能用于实时读，只能保证最终的一致性。适用于读多写少的场景。


<a id='ConcurrentHashMap'></a>
### 1.2 ConcurrentHashMap  
&emsp;&emsp;ConcurrentHashMap是线程安全的的Map容器，在JDK 1.8之前的版本采用的是分段锁的机制来提高并发度，后由于JDK 的synchronzied做了很多的优化，因此JDK 1.8舍弃了之前的segment分段锁，直接采用CAS机制+synchronzied来支持并发操作。  
　　ConcurrentHashMap和HashMap的底层很相似，也是Node<K,V>类型的数组，只不过在值val和指向下一个节点的指针next字段上加了volatile进行修饰。

```java
static class Node<K,V> implements Map.Entry<K,V> {
    final int hash;
    final K key;
    volatile V val;
    volatile Node<K,V> next;

    Node(int hash, K key, V val, Node<K,V> next) {
        this.hash = hash;
        this.key = key;
        this.val = val;
        this.next = next;
    }
    ...
    ...
```

<a id='mapDifference'></a>
#### 1.2.1 ConcurrentHashMap和HashMap的区别
1. ConcurrentHashMap是线程安全的，而HashMap是线程不安全的
2. ConcurrentHashMap不允许key和value为null，而HashMap允许key和value为null


<a id='ConcurrentHashMapPutMethod'></a>
#### 1.2.2 put方法

```java
public V put(K key, V value) {
    return putVal(key, value, false);
}

final V putVal(K key, V value, boolean onlyIfAbsent) {
    if (key == null || value == null) throw new NullPointerException();
    int hash = spread(key.hashCode());  // 计算hashhash码
    int binCount = 0;
    for (Node<K,V>[] tab = table;;) {
        Node<K,V> f; int n, i, fh;
        if (tab == null || (n = tab.length) == 0) // 判断table是否为空，为空时进行初始化
            tab = initTable();
        else if ((f = tabAt(tab, i = (n - 1) & hash)) == null) {
            if (casTabAt(tab, i, null, new Node<K,V>(hash, key, value, null)))
                break;   // no lock when adding to empty bin
        }
        else if ((fh = f.hash) == MOVED)
            tab = helpTransfer(tab, f);
        else {
            V oldVal = null;
            synchronized (f) {
                if (tabAt(tab, i) == f) {
                    if (fh >= 0) {
                        binCount = 1;
                        // 对该桶进行遍历
                        for (Node<K,V> e = f;; ++binCount) {
                            K ek;
                            // 如果找到了直接赋值
                            if (e.hash == hash &&
                                ((ek = e.key) == key ||
                                 (ek != null && key.equals(ek)))) {
                                oldVal = e.val;
                                if (!onlyIfAbsent)
                                    e.val = value;
                                break;
                            }
                            // 没找到，将pred赋值为前驱节点，然后e指向下一个节点，遍历链表
                            Node<K,V> pred = e; 
                            if ((e = e.next) == null) {
                                pred.next = new Node<K,V>(hash, key,
                                                          value, null);
                                break;
                            }
                        }
                    }
                    // 桶是红黑树类型的操作
                    else if (f instanceof TreeBin) {
                        Node<K,V> p;
                        binCount = 2;
                        if ((p = ((TreeBin<K,V>)f).putTreeVal(hash, key,
                                                       value)) != null) {
                            oldVal = p.val;
                            if (!onlyIfAbsent)
                                p.val = value;
                        }
                    }
                }
            }
            if (binCount != 0) {
                // 判断是否需要将该元素所在的桶转化为树
                if (binCount >= TREEIFY_THRESHOLD)
                    treeifyBin(tab, i);
                if (oldVal != null)
                    return oldVal;
                break;
            }
        }
    }
    addCount(1L, binCount);
    return null;
}
```
&emsp;&emsp;ConcurrentHashMap在put操作上和HashMap差别不多，主要差别在多了一些同步的机制：  
　　1.首先也要通过key值来计算hash码。然后判断table是否为空，table为空时，需要调用initTable()进行初始化。  
　　2.然后也是通过(n - 1) & hash来确定新元素在table中的位置，当该位置为null时，通过CAS方式来创建新的Node<K,V>对象，并放在table中的i位置上，这一步不需要加锁。  
　　3.如果该位置不为空，需要对该位置的桶加锁，后续的操作和HashMap类似，这里不再详细讨论。


<a id='blockingQueue'></a>
### 1.n BlockingQueue
BlockingQueue有如下子类：
```java
ArrayBlockingQueue(底层为Object数组)
LinkedBlockingQueue(底层为链表)
DelayQueue(底层为Object数组,不允许插入null值)
PriorityBlockingQueue(底层为Object数组，不允许插入null值，插入的对象需要实现Compareable接口)
SynchronousQueue(不允许插入null值，仅允许容纳一个元素，当一个线程插入一个元素后就会被阻塞，直到元素被另一个线程消费)
```

BlockingQueue调用不同API后的结果如下图（抛出异常、返回特殊值等）：

| - | Throws Exception | Special Value | Bolcks |  Times Out |
| --- | --- | --- | --- | --- |
| Insert | add(o) | offer(o) | put(o) | offer(o,timeout,timeunit) |
| Remove | remove(o) | poll | take() | poll(timeout,timeunit) |
| Examine | element() | peek() |  |  |




<a id='concurrentTools'></a>
## 2.同步组件


<a id='AQSIntr'></a>
### 2.1 AQS介绍(AbstractQueuedSynchronizer)
&emsp;&emsp;首先对AQS做一个简单的介绍，AQS即“抽象队列同步器”，它定义了一套多线程访问共享资源的同步器框架。AQS的功能可以分为两类：独占锁（ReentrantLock）和共享锁（Semaphore、CountDownLatch）。它的所有子类中，要么实现并使用了它独占锁的API，要么使用了共享锁的API，而不会同时使用两套API。AbstractQueuedSynchronizer类中有几个重要的部分先来看一看：

```java
public abstract class AbstractQueuedSynchronizer
    extends AbstractOwnableSynchronizer
    implements java.io.Serializable {

    // AbstractQueuedSynchronizer中的内部类，Node组成了AQS的等待队列
    static final class Node {
        /** Marker to indicate a node is waiting in shared mode */
        static final Node SHARED = new Node();
        /** Marker to indicate a node is waiting in exclusive mode */
        static final Node EXCLUSIVE = null;

        volatile int waitStatus;
        volatile Node prev;  // 该节点的前驱节点
        volatile Node next;  // 该节点的后继节点
        volatile Thread thread;  // 该节点对应的线程
        ...
        ...
    }
    // 等待队列的头节点
    private transient volatile Node head;
    // 等待队列的尾节点
    private transient volatile Node tail;
    /* The synchronization state. */
    private volatile int state;
    ...
    ...
}
```
![CLH](https://github.com/TimePickerWang/ContradictoryBattle/blob/master/images/clh.jpg?raw=true)

&emsp;&emsp;AbstractQueuedSynchronizer内部维持了一个上图所示的等待队列和一个state变量，等待队列由上面代码中的Node对象组成，其实是一个双向链表。现在以非公平的ReentrantLock为例，一般的加锁流程如下：  
　　1.假设现在没有进行任何加锁的操作，某时刻A线程调用ReentrantLock的lock()方法进行加锁，state最初为0，首先利用CAS操作将state变为1，并将当前线程设置为独占线程。  
　　2.当再有线程来进行加锁操作时，先会判断state是否为0，为0则进行第1部操作。  
　　这时很显然state不为0，接着再判断此时的此事的线程是否是当前的独占线程，如果是，即现在进行加锁的线程仍然是A线程，则把state加1，这也说明为什么ReentrantLock是可从入锁。释放锁时，直到state的值变为0时，锁才能够被完全释放。  
　　如果现在加锁的是另一个线程B，则new一个Node对象，并将线程B赋值为Node对象的thread字段。接着会判断此时等待队列的尾节点是否为null，如果不为null，则将该节点插入到等待队列的尾部。很显然此时等待队列的尾节点就是null，因此会new一个头节点，此时头节点就是尾节点，然后该Node赋值为头节点的后继节点和队列的尾节点，当再有新的线程尝试加锁时会直接在队列的尾部添加新的Node。


<a id='ReentrantLock'></a>
### 2.2 ReentrantLock
**ReentrantLock和synchronized的区别**  
　　1.**是否公平**：ReentrantLock可以指定是公平锁还是非公平锁，而synchronized只能是非公平锁。  
　　2.**锁的通知**：使用ReentrantLock时，提供了Condition类，可以分组调用signal()或await()方法，实现选择性通知。  
　　3.**锁的中断**：ReentrantLock提供能够中断等待锁的线程的机制，lock.lockInterruptibly()。如果使用 synchronized ，假设线程A获取了对象O的锁，如果线程A不释放，线程B将一直等下去，不能被中断。  
　　4.**锁的释放**：释放锁时ReentrantLock必须手动进行释放(一般在finally代码块中)，而用synchronized时不需要用户去手动释放锁，要么等持有锁的线程执行完，要么该线程执行发生异常，锁才会被释放。  


<a id='ReentrantReadWriteLock'></a>
### 2.3 ReentrantReadWriteLock


<a id='CountDownLatch'></a>
### 2.4 CountDownLatch



<a id='Semaphore'></a>
### 2.5 Semaphore


<a id='CyclicBarrier'></a>
### 2.6 CyclicBarrier
**CyclicBarrier和CountDownLatch的区别**：  
　　1.CountDownLatch的计数器只能使用一次；CyclicBarrier的计数器可以使用reset()重置，循环使用。  
　　2.CountDownLatch描述的是1个或多个线程等待其它线程的关系；CyclicBarrier描述的是多个线程相互等待，直到满足条件后才能继续执行后续操作。





<a id='threadPool'></a>
## 3.线程池


<a id='threadPoolAdv'></a>
### 3.1 线程池的优点
**1.降低资源消耗**。通过复用已存在的线程和降低线程关闭的次数来尽可能降低系统性能损耗。  
**2.提升系统响应速度**。通过复用线程，省去创建线程的过程，因此整体上提升了系统的响应速度。  
**3.提高线程的可管理性**。线程是稀缺资源，如果无限制的创建，不仅会消耗系统资源，还会降低系统的稳定性，因此，需要使用线程池来管理线程。  




<a id='ThreadPoolExecutor'></a>
### 3.2 ThreadPoolExecutor
**ThreadPoolExecutor的重要参数**：

```java
corePoolSize:核心线程数量
maximumPoolSize:线程最大线程数
workQueue:阻塞队列，存储等待执行的任务，很重要，会对线程池运行过程产生重大影响

keepAliveTime:线程没有任务执行时最多保持多久时间终止（如果线程池中的线程数大于corePoolSize，如果没有新的任务提交，核心线程数之外的线程不会立即销毁，会等待keepAliveTime）
unit:keepAliveTime的时间单位
threadFactory:线程工厂，用来创建线程

rejectHandler:饱和策略，如果workQueue满了，且没有空闲线程处理任务时候，有四个策略：
              1.AbortPolicy          直接拒绝任务，并抛出RejectedExecutionException异常（默认）
              2.CallerRunsPolicy     用调用者所在的线程执行任务
              3.DiscardOldestPolicy  丢弃掉阻塞队列中存放时间最久的任务，执行当前任务
              4.DiscardPolicy        直接丢弃该任务
```


<a id='corePoolSizeRela'></a>
#### 3.2.1 线程池执行流程

![CLH](https://github.com/TimePickerWang/ContradictoryBattle/blob/master/images/liucheng.jpg?raw=true)

&emsp;&emsp;如果运行的线程数少于corePoolSize，即使线程池中有空闲的线程，也会创建新的线程来处理任务  
　　如果运行的线程数大于等于corePoolSize，小于maximumPoolSize，判断当前阻塞队列是否已满，如果未满，则将提交的任务放置在阻塞队列中  
　　如果运行的线程数量大于maximumPoolSize，且workQueue已满，通过饱和策略的参数制定策略去出来任务  
　　如果corePoolSize=maximumPoolSize，则线程池的大小是固定的。如果有新的任务提交且workQueue没满，就把任务放到workQueue里面，等待有空闲线程之后从workQueue里取出进行处理  
　　如果需要降低系统消耗，可以设置较大的workQueue容量和较小的线程池容量，会降低线程处理任务的吞吐量。


<a id='workQueue'></a>
#### 3.2.2 workQueue的三种处理方式
&emsp;&emsp;直接切换：基于SynchronousQueue  
　　使用无界队列：基于LinkedBlockingQueue，线程池中能创建的最大线程数是corePoolSize，maximumPoolSize不会起作用。当线程池中所有核心线程都在运行时，新的任务提交后放到workQueue中。  
　　使用有界队列：基于ArrayBlockingQueue，将线程池中能创建的最大线程数限制为maximumPoolSize，可以降低资源消耗，但因为线程池和workQueue的容量都有限，会使线程池对线程调度变得更困难。  


<a id='threadPoolConf'></a>
#### 3.2.3 线程池的合理配置
&emsp;&emsp;CPU密集型任务，就要尽量压榨CPU，参考值可以设为$n_{cpu} + 1$  
　　IO密集型任务，参考值可以设为$2n_{cpu}$



<a id='threadPoolStatus'></a>
### 3.3 线程池的状态
![pool](https://github.com/TimePickerWang/ContradictoryBattle/blob/master/images/pool.jpg?raw=true)

SHUTDOWN：当线程池处于该状态时，不会接收新的任务，但会处理workQueue中阻塞的任务。在线程池处于RUNNINT状态时，调用shutdown()方法会使线程池进入该状态。

STOP：当线程池处于该状态时，不接受新任务，也不处理workQueue中的任务，会中断正在处理任务的线程。在线程池处于RUNNINT、SHUTDOWN状态时，调用shutdownNow()方法会使线程池进入该状态。


<a id='threadPoolMethod'></a>
### 3.4 ThreadPoolExecutor提供的方法

```java
execute():                 提交任务，交给线程池执行
submit():                  提交任务，能够返回执行结果（execute + Future）
shutdown():                关闭线程池，等待任务都执行完
shutdownNow():             关闭线程池，不等待任务执行完

getTaskCount():            线程池已执行和未执行的任务总数
getCompletedTaskCount():   已完成的任务数量
getPoolSize():             线程池当前的线程数量
getActiveCount():          当前线程池中正在执行任务的线程数量
```


<a id='4threadPool'></a>
### 3.5 Executor提供的四种线程池
```java
Executor.newCachedThreadPool:创建一个可缓存的线程池，若线程池的大小超过了需要，可以灵活回收线程
Executor.newFixedThreadPool:创建一个大小固定的线程池，可以控制线程的最大并发数
Executor.newScheduledThreadPool:创建一个大小固定的线程池，支持定时及周期性的执行
Executor.newSingleThreadPool:创建一个单线程化的线程池，只会用公用的线程执行任务，保证指定任务按
                             指定顺序（先进先出、优先级等）执行
```


<a id='Concurrentexpand'></a>
## 4.并发拓展

<a id='SpringAdnThreadSafe'></a>
### 4.1 Spring与线程安全
&emsp;&emsp;Spring中默认的bean都是单例的，那么是怎么保证线程安全的呢？  
　　是因为我们交由spring管理的大多数对象都是无状态的对象（即没有在bean中声明有状态的实例变量或类变量，平时使用的dao、service、controller等都是无状态对象），我们使用时只是简单的使用，并不涉及到修改bean内部的属性，因此不会出现多个线程修改同一个变量的场景。  
　　如果我们必须要在bean中声明有状态的实例变量或类变量，让对象变成一个有状态的对象的时候，可以使用ThreahLocal，从而把变量变成改线程私有的。如果定义的实例变量或类变量需要在多个线程之间共享，只能使用synchronized，Lock或CAS来实现同步。



<a id='volatile'></a>
### 4.2 volatile关键字



<a id='javaIO'></a>
# 四、BIO、NIO、AIO

<a id='BIO'></a>
## 1.BIO

![pool](https://github.com/TimePickerWang/ContradictoryBattle/blob/master/images/BIO.jpg?raw=true)

&emsp;&emsp;采用BIO通信模型的服务端，通常由一个独立的Acceptor线程负责监听客户端的连接，它接收到客户端的连接请求之后为每个客户端创建一个新的线程进行链路处理，处理完成之后，通过输出流返回应答给客户端，线程销毁，这就是典型的一请求一应答通信模型。  
　　该模型最大的问题就是缺乏弹性伸缩能力，当客户端并发访问量增加后，服务端的线程个数和客户端并发访问数呈1:1的正比关系，由于线程是Java虚拟机非常宝贵的系统资源，当线程数膨胀之后，系统的性能将急剧下降，随着并发访问量的继续增大，系统会发生线程堆栈溢出、创建新线程失败等问题，并最终导致进程宕机或者僵死，不能对外提供服务。



<a id='fakeSynIo'></a>
## 2.伪异步I/O
![pool](https://github.com/TimePickerWang/ContradictoryBattle/blob/master/images/fakeSynIO.jpg?raw=true)

&emsp;&emsp;为了解决同步阻塞I/O面临的一个链路需要一个线程处理的问题，后端可以通过一个线程池来处理多个客户端的请求接入，形成客户端个数M：线程池最大线程数N的比例关系，其中M可以远远大于N，通过线程池可以灵活的调配线程资源。  
　　当有新的客户端接入时，将客户端的Socket封装成一个Task(该任务实现Java.lang.Runnablle接口)投递到后端的线程池中进行处理，JDK的线程池维护一个阻塞队列和N个活跃线程对阻塞队列中的任务进行处理。由于线程池可以设置阻塞队列的大小和最大线程数(FixedThreadPool)，因此，它的资源占用是可控的，无论多少个客户端并发访问，都不会导致资源的耗尽和宕机。 由于线程池和阻塞队列都是有界的，因此，无论客户端并发连接数多大，它都不会导致线程个数过于膨胀或者内存溢出，相对于传统的一连接一线程模型，是一种改良。  
　　伪异步I/O通信框架采用了线程池实现，因此避免了为每个请求都创建一个独立线程造成的线程资源耗尽问题。但是由于它底层的通信依然采用同步阻塞模型，因此无法从根本上解决问题。  



<a id='NIO'></a>
## 3.NIO
&emsp;&emsp;在JDK1.4中引入的NIO解决了阻塞的问题。NIO提供了与传统BIO模型中的Socket和ServerSocket相对应的SocketChannel和ServerSocketChannel两种不同的套接字通道实现。新增的着两种通道都支持阻塞和非阻塞两种模式。阻塞模式使用非常简单，但是性能和可靠性都不好，非阻塞模式则正好相反。我们可以根据自己的需求来选择合适的模式，一般来说，低负载、低并发的应用程序可以选择同步阻塞IO以降低编程复杂度，但是对于高负载、高并发的网络应用，需要使用NIO的非阻塞模式进行开发。首先对NIO中一些新的概念进行一下介绍：  

<a id='Buffer'></a>
### 3.1 缓冲区Buffer
&emsp;&emsp;在NIO库中，所有数据都是用缓冲区处理的。在读取数据时，它直接读到缓冲区中的；在写入数据时，写入到缓冲区中，任何时候访问NIO中的数据，都是通过缓冲区进行操作。缓冲区实质上是一个数组，但又不仅仅是一个数组，它还提供了对数据的结构化访问以及维护读写位置等信息。具体的缓存区有这些：ByteBuffe、 ShortBuffer、IntBuffer、LongBuffer、FloatBuffer、DoubleBuffer、CharBuffer，他们实现了相同的接口：Buffer。  

```java
缓冲区存取数据的两个核心方法：
    put() : 存入数据到缓冲区中
    get() : 获取缓冲区中的数据

缓冲区中的四个核心属性：
    capacity : 容量，表示缓冲区中最大存储数据的容量。一旦声明不能改变。
    limit : 界限，表示缓冲区中可以操作数据的大小。（limit 后数据不能进行读写）
    position : 位置，表示缓冲区中正在操作数据的位置。
    mark : 标记，表示记录当前 position 的位置。可以通过 reset() 恢复到 mark 的位置
四个量间的关系：0 <= mark <= position <= limit <= capacity

直接缓冲区与非直接缓冲区：
    非直接缓冲区：通过 allocate() 方法分配缓冲区，将缓冲区建立在 JVM 的内存中
    直接缓冲区：通过 allocateDirect() 方法分配直接缓冲区，将缓冲区建立在物理内存中。可以提高效率
```

<a id='Channel'></a>
### 3.2 通道Channel
&emsp;&emsp;Channel是一个通道，本身不存储数据，可以通过它读取和写入数据，它就像自来水管一样，网络数据通过Channel读取和写入。通道与流的不同之处在于通道是双向的，流只是一个方向上移动(一个流必须是InputStream或者OutputStream的子类)，而且通道可以用于读、写和同时读写操作。同时Channel是全双工的，因此它可以比流更好的映射底层操作系统的API。  

```java
通道的主要实现类:
java.nio.channels.Channel 接口：
        |--FileChannel(本地文件传输)
        |--SocketChannel(TCP)
        |--ServerSocketChannel(TCP)
        |--DatagramChannel(UDP)

获取通道的方式：
    1. Java 针对支持通道的类提供了 getChannel() 方法
            本地 IO：
            FileInputStream/FileOutputStream
            RandomAccessFile

            网络IO：
            Socket
            ServerSocket
            DatagramSocket

    2. 在 JDK 1.7中的NIO.2针对各个通道提供了静态方法 open()
    3. 在 JDK 1.7中的NIO.2的Files工具类的 newByteChannel()
```


<a id='Selector'></a>
### 3.3 多路复用器Selector
&emsp;&emsp;多路复用器Selector是Java NIO编程的基础，多路复用器提供选择已经就绪的任务的能力，简单的说，Selector会不断的轮询注册在其上的Channel，如果某个Channel上面有新的TCP连接接入、读和写事件，这个Channel就处于就绪状态，会被Selector轮询出来，然后通过SelectionKey可以获取就绪Channel的集合，进行后续的I/O操作。  
　　一个多用复用器Selector可以同时轮询多个Channel，由于JDK使用了epoll()代替传统的select实现，所以它并没有最大连接句柄1024/2048的限制，这也意味着只需要一个线程负责Selector的轮询，就可以接入成千上万的客户端。  



<a id='IOTimePic'></a>
### 3.4 服务端和客户端的通讯时序图

![pool](https://github.com/TimePickerWang/ContradictoryBattle/blob/master/images/NIO.jpg?raw=true)


<a id='NIOAdvantage'></a>
### 3.5 NIO的优点
(1)客户端发起的连接操作是异步的，可以通过在多路复用器注册OP_CONNECT等后续结果，不需要像之前的客户端那样被同步阻塞。  
(2)SocketChannel的读写操作都是异步的，如果没有可读写的数据它不会同步等待，直接返回，这样IO通信线程就可以处理其它的链路，不需要同步等待这个链路可用。  
(3)线程模型的优化：由于JDK的Selector在Linux等主流操作系统上通过epoll实现，它没有连接句柄数的限制(只受限于操作系统的最大句柄数或者对单个进程的句柄限制)，这意味着一个Selector线程可以同时处理成千上万个客户端连接，而且性能不会随着客户端的增加而线性下降，因此，它非常适合做高性能、高负载的网络服务器。  


<a id='AIO'></a>
## 4.AIO
&emsp;&emsp;DK1.7升级了NIO类库，升级后的NIO类库被称为NIO2.0。也就是我们要介绍的AIO。NIO2.0引入了新的异步通道的概念，并提供了异步文件通道和异步套接字通道的实现。异步通道提供两种方式获取操作结果。  
　　(1)通过Java.util.concurrent.Future类来表示异步操作的结果。  
　　(2)在执行异步操作的时候传入一个Java.nio.channels。  
　　CompletionHandler接口的实现类作为操作完成的回调。  
　　NIO2.0的异步套接字通道是真正的异步非阻塞IO，它对应UNIX网络编程中的事件驱动IO(AIO)，它不需要通过多路复用器(Selector)对注册的通道进行轮询操作即可实现异步读写，从而简化了NIO的编程模型。  
　　我们可以得出结论：异步Socket Channel是被动执行对象，我们不需要想NIO编程那样创建一个独立的IO线程来处理读写操作。对于AsynchronousServerSocketChannel和AsynchronousSocketChannel，它们都由JDK底层的线程池负责回调并驱动读写操作。正因为如此，基于NIO2.0新的异步非阻塞Channel进行编程比NIO编程更为简单。


**IO模型的对比**
![pool](https://github.com/TimePickerWang/ContradictoryBattle/blob/master/images/IOCampare.jpg?raw=true)


<a id='reference'></a>
# reference
[一、BIO、NIO、AIO通信机制理解](https://blog.csdn.net/l_kanglin/article/details/71531761)  
[Java 网络IO编程总结（BIO、NIO、AIO均含完整实例代码）](https://blog.csdn.net/anxpp/article/details/51512200)  


