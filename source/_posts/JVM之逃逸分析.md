---
title: "JVM之逃逸分析"
catalog: true
date: 2019-05-23 20:42:52
subtitle: "This is about JVM."
header-img: "Demo.png"
tags:
- JVM
- 逃逸分析
catagories:
- JVM
---

# JVM之逃逸分析

## 1.即时编译技术

Java 程序运行时，JVM 会将 `.class` 字节码转换成机器能够识别的指令，指令转换过程会产生耗时，延缓程序的运行速度，为了解决这种问题出现了「JIT（即时编译）」技术。JIT 主要有两个功能：

- 缓存「Hot Spot Code（热点代码：频繁运行的方法或代码块）」对应的机器指令，方便下次调用。
- 代码编译优化。

而在 JIT 的代码优化过程中，最重要的就是「逃逸分析（Escape Analysis）」

## 2.逃逸分析

逃逸分析就是 **分析Java对象的动态作用域**。

当一个对象被定义之后，可能会被外部对象引用，称之为「方法逃逸」；也有可能被其他线程所引用，称之为「线程逃逸」。

```java
public class EscapeObject {
    public static String createStr() {
        String sb = "hello world!";
        return sb;
    }
}
```

例如上面这段代码将创建的字符串对象 sb 返回，这样可以被其他方法或线程引用。

```java
public class EscapeObject {
    public static String createStr() {
        StringBuffer sb = new StringBuffer("hello world!");
        return sb.toString();
    }
}
```

如果这样实现的话，sb 对象就没有「逃逸」。

利用逃逸分析，编译器可以对代码做如下优化：

1. **同步省略**

   如果一个对象被发现只能从一个线程被访问到，那么对于这个对象的操作可以不考虑同步

2. **标量替换（或分离对象）**

   有的对象可能不需要作为一个连续的内存结构存在也可以被访问到，那么对象的部分（或全部）可以不存储在内存，而是存储在CPU寄存器中

3. **栈上分配**

   将堆分配转化为栈分配。如果一个对象在子程序中被分配，要使指向该对象的指针永远不会逃逸，对象可能是栈分配的候选，而不是堆分配（但是不是真正的栈分配）



逃逸分析可以有效减少Java程序中同步负载和内存堆分配压力的跨函数全局数据流分析算法。

通过逃逸分析，Java Hotspot编译器能够分析出一个新的对象的引用的使用范围从而决定是否要将这个对象分配到堆上

### 2.1 同步省略

在 JIT 编译过程中，如果发现一个对象**不会被多线程访问**，那么针对这个对象的同步措施就可以省略掉，即「锁销除」。例如 Vector 和 StringBuffer 这样的类，它们中的很多方法都是有锁的，当某个对象确定是线程安全的情况下，JIT编译器会在编译这段代码时进行锁销除来提升效率。

### 2.2 标量替换

「标量（Scalar）」是指无法再分解成更小粒度的数据，例如 Java 中的原始数据类型（int，long等），相对如果一个数据可以继续分解，则称之为「聚合量（Aggregate）」，例如 Java对象。在 JIT 编译过程中，经过逃逸分析确定一个对象不会被其他线程或者方法访问，那么会将对象的创建替换成为多个成员变量的创建，称之为「标量替换」。

```java
public class EscapeObject {

    private static void getUser() {
        User user = new User("张三", 18);
        System.out.println("user name is " + user.name + ", age is " + user.age);
    }

    public static void main(String[] args) {
        getUser();
    }
}

class User {
    String name;
    int age;

    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```

上面这段代码中，对象 user 只会在`getUser()`方法中被调用，那么 JIT动态编译时，不会创建对象 user，而之创建它的两个成员变量 name 和 age，类似：

```java
private static void getUser() {
    String name = "张三";
    int age = 18;

    System.out.println("user name is " + user.name + ", age is " + user.age);

}

public static void main(String[] args) {
    getUser();
}
```

标量替换减少了创建对象需要的堆内存，同时也不用进行 GC。

### 2.3 栈上分配

「栈上分配」是指对象和数据不是创建在堆上，而是创建在栈上，随着方法的结束自动销毁。**但实际上，JVM 例如常用的「HotSpot」虚拟机并没有实现栈上分配，实际是用「标量替换」代替实现的。**

在 JAVA 中，对象只分配在堆中：

> The heap is the runtime data area from which memory for all class instances and arrays is allocated。
>
> 堆是所有的对象实例以及数组分配内存的运行时数据区域。

### 2.4 如何开启逃逸分析

可以通过设置 JVM 参数来开启或关闭逃逸分析

- `-XX:+DoEscapeAnalysis` :开启逃逸分析（从JDK1.7开始默认开启）
- `-XX:-DoEscapeAnalysis` :关闭逃逸分析



## 3.缺陷

无法保证逃逸分析的性能消耗一定能高于他的消耗。

虽然经过逃逸分析可以做标量替换、栈上分配、和锁消除。但是逃逸分析自身也是需要进行一系列复杂的分析的，这其实也是一个相对耗时的过程。

一个极端的例子，就是经过逃逸分析之后，发现没有一个对象是不逃逸的。那这个逃逸分析的过程就白白浪费掉了。



**参考资料：**

https://segmentfault.com/a/1190000016803174

