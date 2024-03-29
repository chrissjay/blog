---
title: "Java并发编程之基础多线程"
catalog: true
date: 2019-05-08 15:21:00
subtitle: "This is about 多线程."
header-img: "Demo.png"
tags:
- 同步
- 多线程
catagories:
- 并发编程
---


# Java并发编程之基础多线程

## 1.操作系统进程/线程状态

在现在的操作系统中，因为线程依旧被视为轻量级进程，所以操作系统中线程的状态实际上和进程状态是一致的模型，以下是操作系统中的进程/线程状态：

![img](https://img-blog.csdnimg.cn/20190810145817211.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Nocmlzc3c=,size_16,color_FFFFFF,t_70)

从实际意义上来讲，操作系统中的线程除去new和terminated状态，一个线程真实存在的状态，只有：

- ready：表示线程已经被创建，正在等待系统调度分配CPU使用权。
- running：表示线程获得了CPU使用权，正在进行运算
- waiting：表示线程等待（或者说挂起），让出CPU资源给其他线程使用

## 2.Java线程状态

![å¨è¿éæå¥å¾çæè¿°](https://img-blog.csdnimg.cn/20190810145845387.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Nocmlzc3c=,size_16,color_FFFFFF,t_70)

## 3.对比OS和Java的线程模型

对于Java中的线程状态：

- 无论是`Timed Waiting` ，`Waiting`还是`Blocked`，对应的都是操作系统线程的**waiting（等待）**状态。
- 而`Runnable`状态，则对应了操作系统中的`ready`和`running`状态。

**对不同的操作系统，由于本身设计思路不一样，对于线程的设计也存在种种差异，所以JVM在设计上，就已经声明：虚拟机中的线程状态，不反应任何操作系统线程状态**

## 4.实现多线程的三种基本方式

1. 继承Thread类

   Thread类本质上也是实现了Runnable接口的一个类，它代表一个线程的实例。启动线程的唯一方法就是通过Thread类的start()方法。start()方法是一个native方法，它将启动一个新线程，并执行run()方法。通过自己的类直接extends Thread，并复写run()方法，就可以启动新线程并执行自己定义的run()方法。

2. 实现Runnable接口

    

3. 使用ExecutorService、Callable、Future实现有返回结果的多线程。

其中前两种方式线程执行完后都**没有返回值**，只有最后一种是**带返回值**的。

**对比方法1/2**

Thread 是类，而Runnable是接口；Thread本身是实现了Runnable接口的类。我们知道“一个类只能有一个父类，但是却能实现多个接口”，因此Runnable具有更好的扩展性。此外，Runnable还可以用于“资源的共享”。即，多个线程都是基于某一个Runnable对象建立的，它们会共享Runnable对象上的资源。

### 5.start和run的区别

**start()** : 它的作用是启动一个新线程，新线程会执行相应的run()方法。start()不能被重复调用。 **run()**   : run()就和普通的成员方法一样，可以被重复调用。单独调用run()的话，**会在当前线程中执行run()，而并不会启动新线程！**