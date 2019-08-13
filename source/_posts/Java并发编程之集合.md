---
title: "Java并发编程之集合"
catalog: true
date: 2019-05-17 21:42:31
subtitle: "This is about 集合."
header-img: "Demo.png"
tags:
- 同步
- 多线程
- 集合
catagories:
- Java并发编程
---

# Java并发编程之集合

## 1.List, Set

![03183243-4e08a58a34e940b08bdb330206cd5545](/Users/chriswu/Desktop/Java开发技术栈/pic/03183243-4e08a58a34e940b08bdb330206cd5545.jpg)



1. CopyOnWriteArrayList相当于线程安全的ArrayList，它实现了List接口。CopyOnWriteArrayList是支持高并发的
2. CopyOnWriteArraySet相当于线程安全的HashSet，它继承于AbstractSet类。CopyOnWriteArraySet内部包含一个CopyOnWriteArrayList对象，它是通过CopyOnWriteArrayList实现的

#### 1.copy on write(写时复制)

当我们往一个容器添加元素的时候，不直接往当前容器添加，而是先将当前容器进行Copy，复制出一个新的容器，然后新的容器里添加元素，添加完元素之后，再将原容器的引用指向新的容器。

这样做的好处是我们可以对CopyOnWrite容器进行并发的读，而不需要加锁(**但是修改需要互斥锁**)，因为当前容器不会添加任何元素。所以CopyOnWrite容器也是一种读写分离的思想，读和写不同的容器。

**应用场景**

CopyOnWrite并发容器用于读多写少的并发场景。比如白名单，黑名单，商品类目的访问和更新场景，假如我们有一个搜索网站，用户在这个网站的搜索框中，输入关键字搜索内容，但是某些关键字不允许被搜索。这些不能被搜索的关键字会被放在一个黑名单当中，黑名单每天晚上更新一次。当用户搜索时，会检查当前关键字在不在黑名单当中，如果在，则提示不能搜索。

**弊端**

1. 内存占用问题。因为CopyOnWrite的写时复制机制，所以在进行写操作的时候，**内存里会同时驻扎两个对象的内存**，旧的对象和新写入的对象（注意:在复制的时候只是复制容器里的引用，只是在写的时候会创建新对象添加到新容器里，而旧容器的对象还在使用，所以有两份对象内存）。如果这些对象占用的内存比较大，比如说200M左右，那么再写入100M数据进去，内存就会占用300M，那么这个时候很有可能造成频繁的Yong GC和Full GC。之前我们系统中使用了一个服务由于每晚使用CopyOnWrite机制更新大对象，造成了每晚15秒的Full GC，应用响应时间也随之变长。针对内存占用问题，可以通过压缩容器中的元素的方法来减少大对象的内存消耗，比如，如果元素全是10进制的数字，可以考虑把它压缩成36进制或64进制。或者不使用CopyOnWrite容器，而使用其他的并发容器，如ConcurrentHashMap。
2. 数据一致性问题。CopyOnWrite容器**只能保证数据的最终一致性，不能保证数据的实时一致性**。所以如果你希望写入的的数据，马上能读到，请不要使用CopyOnWrite容器。

#### 2.CopyOnWriteArrayList

1. 它最适合于具有以下特征的应用程序：List 大小通常保持很小，只读操作远多于可变操作，需要在遍历期间防止线程间的冲突。
2. 它是线程安全的。
3. 因为通常需要复制整个基础数组，所以可变操作（add()、set() 和 remove() 等等）的开销很大。
4. 迭代器支持hasNext(), next()等不可变操作，但不支持可变 remove()等操作。
5. 使用迭代器进行遍历的速度很快，并且不会与其他线程发生冲突。在构造迭代器时，迭代器依赖于不变的数组快照。

#### 3.CopyOnWriteArraySet

CopyOnWriteArraySet和HashSet虽然都继承于共同的父类AbstractSet，但是，HashSet是通过“散列表(HashMap)”实现的，而CopyOnWriteArraySet是通过“动态数组(CopyOnWriteArrayList)”实现的，并不是散列表

其余性质同上



## 2.Map

![03183307-35a8caaf56014c9ab176a2b2636c70b8](/Users/chriswu/Desktop/Java开发技术栈/pic/03183307-35a8caaf56014c9ab176a2b2636c70b8.jpg)

1. ConcurrentHashMap是线程安全的哈希表(相当于线程安全的HashMap)；它继承于AbstractMap类，并且实现ConcurrentMap接口。ConcurrentHashMap是通过“锁分段”来实现的，它支持并发
2. ConcurrentSkipListMap是线程安全的有序的哈希表(相当于线程安全的TreeMap); 它继承于AbstractMap类，并且实现ConcurrentNavigableMap接口。ConcurrentSkipListMap是通过“跳表”来实现的，它支持并发
3. ConcurrentSkipListSet是线程安全的有序的集合(相当于线程安全的TreeSet)；它继承于AbstractSet，并实现了NavigableSet接口。ConcurrentSkipListSet是通过ConcurrentSkipListMap实现的，它也支持并发



## 3.Queue

![03183545-ab9686b3fe5944509de9a87b08498ee5](/Users/chriswu/Desktop/Java开发技术栈/pic/03183545-ab9686b3fe5944509de9a87b08498ee5.jpg)

**阻塞队列：**

1. ArrayBlockingQueue是数组实现的线程安全的有界的阻塞队列。
2. LinkedBlockingQueue是单向链表实现的(指定大小)阻塞队列，该队列按 FIFO（先进先出）排序元素。
3. LinkedBlockingDeque是双向链表实现的(指定大小)双向并发阻塞队列，该阻塞队列同时支持FIFO和FILO两种操作方式。
4. PriorityBlockingQueue是一个支持优先级排序的无界阻塞队列。
5. DelayQueue是一个使用优先级队列实现的无界阻塞队列。
6. SynchronousQueue是一个不存储元素的阻塞队列。
7. LinkedTransferQueue是一个由链表结构组成的无界阻塞队列。

**同步队列：**

1. ConcurrentLinkedQueue是单向链表实现的无界队列，该队列按 FIFO（先进先出）排序元素。
2. ConcurrentLinkedDeque是双向链表实现的无界队列，该队列同时支持FIFO和FILO两种操作方式。



#### 1.ArrayBlockingQueue

1. ArrayBlockingQueue内部通过“互斥锁”保护竞争资源，实现了多线程对竞争资源的互斥访问
2. 其数组是有界限的
3. 多线程访问竞争资源时，当竞争资源已被某线程获取时，其它要获取该资源的线程需要阻塞等待
4. 按 FIFO（先进先出）原则对元素进行排序，元素都是从尾部插入到队列，从头部开始返回

##### 1.插入操作

```java
    public boolean add(E e) {return super.add(e);}
    public boolean offer(E e) {
        Objects.requireNonNull(e);
        final ReentrantLock lock = this.lock;
        lock.lock();
        try {
            if (count == items.length)
                return false;
            else {
                enqueue(e);
                return true;}} 
            finally {
            		lock.unlock();}}
```

##### 2.真正的入队

```java
    private void enqueue(E e) {
        // assert lock.isHeldByCurrentThread();
        // assert lock.getHoldCount() == 1;
        // assert items[putIndex] == null;
        final Object[] items = this.items;
        items[putIndex] = e;
        if (++putIndex == items.length) putIndex = 0;
        count++;
        notEmpty.signal();	}
```

这里使用到了condition: "notEmpty"

##### 3.整体逻辑

一直等待获取锁 - > 当获取到锁之后，比较当前的元素个数与数组长度，当相等时，那么队列已经满了，无法插入，返回false->否则进行**入队**操作，返回true。

#### 2.LinkedBlockingQueue

大体上和ArrayBlockingQueue相同，有以下区别：

1. LinkedBlockingQueue还是可选容量的(防止过度膨胀)，即可以指定队列的容量。如果不指定，默认容量大小等于Integer.MAX_VALUE
2. 有两把锁：putLock是插入锁，takeLock是取出锁；notEmpty是“非空条件”，notFull是“未满条件”。通过它们对链表进行并发控制。LinkedBlockingQueue在实现“多线程对竞争资源的互斥访问”时，对于“插入”和“取出(删除)”操作分别使用了不同的锁。对于插入操作，通过“插入锁putLock”进行同步；对于取出操作，通过“取出锁takeLock”进行同步。此外，插入锁putLock和“非满条件notFull”相关联，取出锁takeLock和“非空条件notEmpty”相关联。通过notFull和notEmpty更细腻的控制锁。
3. 因为ArrayBlockingQueue只有一把锁，LinkedBlockingQueue有两把锁，所以LBQ可以一边取一边放，而ABQ不行，**关于为何这样设计，有以下猜测：**
   1. LinkedBlockingQueue的较大一部分时间需要构造节点，导致较长的等待。所以同时存取有较大优化。而ArrayBlockingQueue的不用构造节点，加锁和解锁的时间可能占比较大。
   2. 转成双锁之后，对比原来的存取操作，需要多竞争两次。一次是Atomic变量的cas操作，另一次是获得另一把锁的通知操作。可能这部分的损耗，已经比并发存取带来收益更大

#### 3.LinkedBlockingDeque

双向链表，其余同上

#### 4.PriorityBlockingQueue

PriorityBlockingQueue是一个支持优先级的无界队列。默认情况下元素采取自然顺序排列，也可以通过比较器comparator来指定元素的排序规则。元素按照升序排列

#### 5.DelayQueue

队列使用PriorityQueue来实现。队列中的元素必须实现Delayed接口，在创建元素时可以指定多久才能从队列中获取当前元素。只有在延迟期满时才能从队列中提取元素。我们可以将DelayQueue运用在以下应用场景：

- 缓存系统的设计：可以用DelayQueue保存缓存元素的有效期，使用一个线程循环查询DelayQueue，一旦能从DelayQueue中获取元素时，表示缓存有效期到了。
- 定时任务调度。使用DelayQueue保存当天将会执行的任务和执行时间，一旦从DelayQueue中获取到任务就开始执行，从比如TimerQueue就是使用DelayQueue实现的。

#### 6.SynchronousQueue

每一个put操作必须等待一个take操作，否则不能继续添加元素。它把生产者线程处理的数据直接传递给消费者线程。队列本身并不存储任何元素，非常适合于传递性场景,比如在一个线程中使用的数据，传递给另外一个线程使用，SynchronousQueue的吞吐量高于LinkedBlockingQueue 和 ArrayBlockingQueue。

#### 7.LinkedTransferQueue

是一个由链表结构组成的无界阻塞队列。 

#### 

