---
title: "Java并发编程之锁"
catalog: true
date: 2019-05-15 23:52:01
subtitle: "This is about 锁."
header-img: "Demo.png"
tags:
- 同步
- 多线程
- 锁
catagories:
- Java并发编程
---

# Java并发编程之锁

![271147386096273](./pic/271147386096273.png)

## 1.AQS

AQS是java中管理“锁”的抽象类，锁的许多公共方法都是在这个类中实现。AQS是独占锁(例如，ReentrantLock)和共享锁(例如，Semaphore)的公共父类。

### 1.AQS锁的类别

**分为“独占锁”和“共享锁”两种**

(01) 独占锁 -- 锁在一个时间点只能被一个线程锁占有。根据锁的获取机制，它又划分为“公平锁”和“非公平锁”。公平锁，是按照通过CLH等待线程按照先来先得的规则，公平的获取锁；而非公平锁，则当线程要获取锁时，它会无视CLH等待队列而直接获取锁。独占锁的典型实例子是ReentrantLock，此外，ReentrantReadWriteLock.WriteLock也是独占锁。
(02) 共享锁 -- 能被多个线程同时拥有，能被共享的锁。JUC包中的ReentrantReadWriteLock.ReadLock，CyclicBarrier， CountDownLatch和Semaphore都是共享锁。

### 2.CAS函数 -- Compare And Swap

CAS函数，是比较并交换函数，它是原子操作函数；即，通过CAS操作的数据都是以原子方式进行的。例如，compareAndSetHead(), compareAndSetTail(), compareAndSetNext()等函数。它们共同的特点是，这些函数所执行的动作是以原子的方式进行的。

## 2.Lock

```java
public interface Lock {
    void lock();//尝试获取锁，若不成功则等待
    void lockInterruptibly() throws InterruptedException;//尝试获取锁，可以相应中断
    boolean tryLock();//尝试获取锁，有返回值，不会等待
    boolean tryLock(long time, TimeUnit unit) throws InterruptedException;
    void unlock();
    Condition newCondition();}
```

## 3.ReentrantLock(可重入锁)

synchronized和ReentrantLock都是可重入锁，ReentrantLock是唯一实现了Lock接口的类。

```java
class MyClass {
    public synchronized void method1() { method2();}     
    public synchronized void method2() {}   }
```

某一时刻，线程A执行到了method1，此时线程A获取了这个对象的锁，而由于method2也是synchronized方法，假如synchronized不具备可重入性，此时线程A需要重新申请锁(**即具备可重入性就不需要重新申请锁**)。但是这就会造成一个问题，因为线程A已经持有了该对象的锁，而又在申请获取该对象的锁，这样就会线程A一直等待永远不会获取到的锁。

## 4.ReadWriteLock(读写锁)

ReadWriteLock也是一个接口，在它里面只定义了两个方法：

**readLock()和writeLock()**:用来获取读锁和写锁。ReentrantReadWriteLock实现了ReadWriteLock接口

1. “读取锁”用于只读操作，它是“共享锁”，能同时被多个线程获取。
2. “写入锁”用于写入操作，它是“独占锁”，写入锁只能被一个线程锁获取。**不能同时存在读取锁和写入锁！**
3. 如果一个线程已经占用了读锁，则此时其他线程如果要申请写锁，则申请写锁的线程会一直等待释放读锁。
4. 如果一个线程已经占用了写锁，则此时其他线程如果申请写锁或者读锁，则申请的线程会一直等待释放写锁。

## 5.可中断锁

synchronized就不是可中断锁，而Lock是可中断锁。

## 6.公平锁

**非公平锁即无法保证锁的获取是按照请求锁的顺序进行的**。这样就可能导致某个或者一些线程永远获取不到锁。

在Java中，synchronized就是非公平锁，它无法保证等待的线程获取锁的顺序。

而对于ReentrantLock和ReentrantReadWriteLock，它默认情况下是非公平锁，但是可以设置为公平锁。

## 7.与synchronized对比

1. Lock不是Java语言内置的，synchronized是Java语言的关键字，因此是内置特性。Lock是一个类，通过这个类可以实现同步访问；
2. synchronized不需要用户去手动释放锁，当synchronized方法或者synchronized代码块执行完之后，系统会自动让线程释放对锁的占用；而Lock则必须要用户去手动释放锁，如果没有主动释放锁，就有可能导致死锁

## 8.Condition

Condition的作用是对锁进行更精确的控制。Condition中的await()方法相当于Object的wait()方法，Condition中的signal()方法相当于Object的notify()方法，Condition中的signalAll()相当于Object的notifyAll()方法。

**不同的是，Object中的wait(),notify(),notifyAll()方法是和"同步锁"(synchronized关键字)捆绑使用的；而Condition是需要与"互斥锁"/"共享锁"捆绑使用的。**

## 9.Semaphore(信号量)

允许多个线程同时访问： synchronized 和 ReentrantLock 都是一次只允许一个线程访问某个资源，Semaphore(信号量)可以指定多个线程同时访问某个资源。

## 10.CountDownLatch （倒计时器）

CountDownLatch是一个同步工具类，用来协调多个线程之间的同步。这个工具通常用来控制线程等待，它可以让某一个线程等待直到倒计时结束，再开始执行。

## 11.CyclicBarrier(循环栅栏)

CyclicBarrier 和 CountDownLatch 非常类似，它也可以实现线程间的技术等待，但是它的功能比 CountDownLatch 更加复杂和强大。主要应用场景和 CountDownLatch 类似。CyclicBarrier 的字面意思是可循环使用（Cyclic）的屏障（Barrier）。它要做的事情是，让一组线程到达一个屏障（也可以叫同步点）时被阻塞，直到最后一个线程到达屏障时，屏障才会开门，所有被屏障拦截的线程才会继续干活。CyclicBarrier默认的构造方法是 CyclicBarrier(int parties)，其参数表示屏障拦截的线程数量，每个线程调用await方法告诉 CyclicBarrier 我已经到达了屏障，然后当前线程被阻塞。