---
title: "Java并发编程之原子类"
catalog: true
date: 2019-05-18 00:14:046
subtitle: "This is about 原子类."
header-img: "Demo.png"
tags:
- 同步
- 多线程
- 原子类
catagories:
- Java并发编程
---

# Java并发编程之原子类

## 1.AtomicInteger

**(数组/对象原理均相同)**

AtomicInteger是对int类型的一个封装，提供原子性的访问和更新操作，其原子性操作的实现是基于CAS（compare-and-swap）技术。

从AtomicInteger的内部属性可以看出，它依赖于Unsafe提供的一些底层能力，进行底层操作

以volatile的value字段，记录数值，以保证可见性。

**乐观锁实现，volatile+CAS实现，更新不成功继续循环更新，效率比每次都加锁(悲观锁)高很多的。**

```java
private static final jdk.internal.misc.Unsafe U = jdk.internal.misc.Unsafe.getUnsafe();
private static final long VALUE = U.objectFieldOffset(AtomicInteger.class, "value");
private volatile int value;
```

## 2.AtomicLongFieldUpdater

AtomicLongFieldUpdater可以对指定"类的 'volatile long'类型的成员"进行原子更新。它是基于反射原理实现的。