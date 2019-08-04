---
title: "Java基础之泛型"
catalog: true
date: 2019-05-05 18:24:01
subtitle: "This is about java."
header-img: "Demo.png"
tags:
- Java 基础
catagories:
- Java 基础
---

# Java基础之泛型

### 1.概念（1.5引入）

**类型参数**，这样的代码具有更好的**可读性**。编译器会自动帮我们检查，避免向集合中插入错误类型的对象，从而使得程序具有更好的**安全性，可以将运行时错误提前到编译时错误。**

### 2.通配符

`List<? extends T>`可以接受任何继承自T的类型的List，而`List<? super T>`可以接受任何T的父类构成的List。例如`List<? extends Number>`可以接受`List<Integer>`或List`<Float>`

### 3.类型擦除

#### 1.概念

泛型信息只存在于代码编译阶段，在进入 JVM 之前，与泛型相关的信息会被擦除掉

```java
List<String> l1 = new ArrayList<String>();
List<Integer> l2 = new ArrayList<Integer>();
System.out.println(l1.getClass() == l2.getClass());
```

打印的结果为 true 是因为 `List<String>`和 `List<Integer>`在 jvm 中的 Class 都是 List.class

#### 2.局限性

类型擦除，让泛型能够与之前的 java 版本代码兼容，但也因为类型擦除，它会抹掉很多继承相关的特性。

```java
public class ToolTest {
	public static void main(String[] args) {
		List<Integer> ls = new ArrayList<>();
		ls.add(23);
//		ls.add("text");
		try {
			Method method = ls.getClass().getDeclaredMethod("add",Object.class);		
			method.invoke(ls,"test");
			method.invoke(ls,42.9f);
		} catch (NoSuchMethodException e) {}
	}
}
```

利用类型擦除的原理，用反射的手段就绕过了正常开发中编译器不允许的操作限制(add)

#### 3.不支持数组

同样是类型擦除的影响

### 4.细节问题

#### 1.为什么泛型只支持包装类？

没有`ArrayList<double>`，只有`ArrayList<Double>`。因为当类型擦除后，ArrayList的原始类中的类型变量（T）替换为Object，但Object类型不能存储double值。

#### 2.Array中可以用泛型吗?

Array并不支持泛型，这也是为什么Joshua Bloch在Effective Java一书中建议使用List来代替Array，因为List可以提供编译期的类型安全保证，而Array却不能。

#### 3.Java中`List<?>`和`List<Object>`之间的区别是什么?

`List<?>` 是一个未知类型的List，而`List<Object>`其实是任意类型的List。你可以把`List<String>`, `List<Integer>`赋值给`List<?>`，却不能把`List<String>`赋值给`List<Object>`

