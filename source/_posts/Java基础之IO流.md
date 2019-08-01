---
title: "Java基础之IO/流"
catalog: true
date: 2019-05-03 21:44:00
subtitle: "This is about java."
header-img: "Demo.png"
tags:
- Java 基础
catagories:
- Java 基础
---

# Java基础之IO/流

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190801233719954.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Nocmlzc3c=,size_16,color_FFFFFF,t_70)

## 1.字节，字符，缓冲流

**字节流在操作时本身不会用到缓冲区（内存），是文件本身直接操作的，而字符流在操作时使用了缓冲区，通过缓冲区再操作文件**

如果想在不关闭时也可以将字符流的内容全部输出，则可以使用Writer类中的flush()方法完成。

字节流是最基本的，所有的InputStream和OutputStream的子类都是,主要用在处理二进制数据，它是按字节来处理的。但实际中很多的数据是文本，又提出了字符流的概念，它是按虚拟机的encode来处理，也就是要进行字符集的转化。这两个之间通过 InputStreamReader,OutputStreamWriter来关联，实际上是通过byte[]和String来关联。在实际开发中出现的汉字问题实际上都是在字符流和字节流之间转化不统一而造成的

**字节缓冲流BufferedInputStream，BufferedOutputStream**

在创建 BufferedInputStream时，会创建一个内部缓冲区数组。在读取流中的字节时，可根据需要从包含的输入流再次填充该内部缓冲区，一次填充多个字节。

在使用`BufferedOutputStream`写完数据后，要调用`flush()`方法或`close()`方法，强行将缓冲区中的数据写出。否则可能无法写出数据。与之相似还`BufferedReader`和`BufferedWriter`两个类。

**字节缓冲流使用了"装饰者"设计模式**

BufferedInputStream为装饰者类，FileInputStream为被装饰者类，前者的作用就是为了加强后者已有的功能，这里就是为了提高数据流的读写效率。

## 2.Java NIO BIO AIO

1. BIO，同步阻塞式IO，简单理解：一个连接一个线程
2. NIO，同步非阻塞IO，简单理解：一个请求一个线程
3. AIO，异步非阻塞IO，简单理解：一个有效请求一个线程