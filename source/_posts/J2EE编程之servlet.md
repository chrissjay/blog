---
title: "J2EE编程之servlet"
catalog: true
date: 2019-06-28 19:21:00
subtitle: "This is about J2EE."
header-img: "Demo.png"
tags:
- J2EE
- servlet
catagories:
- J2EE

---

# J2EE编程之servlet

在Java Web程序中，**Servlet**主要负责接收用户请求**HttpServletRequest**,在**doGet()**,**doPost()**中做相应的处理，并将回应**HttpServletResponse**反馈给用户。Servlet可以设置初始化参数，供Servlet内部使用。一个Servlet类只会有一个实例，在它初始化时调用**init()方法**，销毁时调用**destroy()方法**。**Servlet需要在web.xml中配置**（MyEclipse中创建Servlet会自动配置），**一个Servlet可以设置多个URL访问**。**Servlet不是线程安全**，因此要谨慎使用类变量。

## 1.servlet生命周期

1. 生命周期：**Web容器加载Servlet并将其实例化后，Servlet生命周期开始**，容器运行其**init()方法**进行Servlet的初始化；请求到达时调用Servlet的**service()方法**，service()方法会根据需要调用与请求对应的**doGet或doPost**等方法；当服务器关闭或项目被卸载时服务器会将Servlet实例销毁，此时会调用Servlet的**destroy()方法**。**init方法和destroy方法只会执行一次，service方法客户端每次请求Servlet都会执行**。Servlet中有时会用到一些需要初始化与销毁的资源，因此可以把初始化资源的代码放入init方法中，销毁资源的代码放入destroy方法中，这样就不需要每次处理客户端的请求都要初始化与销毁资源。

2. 最后，Servlet 是由 JVM 的垃圾回收器进行垃圾回收的。

   ![9ED83913-D5EB-452F-994B-EEEE0E981D08](/Users/chriswu/Desktop/Java开发技术栈/pic/9ED83913-D5EB-452F-994B-EEEE0E981D08.png)

## 2.servlet主要方法

**init() 方法**
init 方法被设计成只调用一次。它在第一次创建 Servlet 时被调用，在后续每次用户请求时不再调用。因此，它是用于一次性初始化，就像 Applet 的 init 方法一样。

**service() 方法**
service() 方法是执行实际任务的主要方法。Servlet 容器（即 Web 服务器）调用 service() 方法来处理来自客户端（浏览器）的请求，并把格式化的响应写回给客户端。

每次服务器接收到一个 Servlet 请求时，服务器会产生一个新的**线程**并调用服务。service() 方法检查 HTTP 请求类型（GET、POST、PUT、DELETE 等），并在适当的时候调用 **doGet、doPost、doPut，doDelete** 等方法。

**destroy() 方法**
destroy() 方法只会被调用一次，在 Servlet 生命周期结束时被调用。destroy() 方法可以让您的 Servlet 关闭数据库连接、停止后台线程、把 Cookie 列表或点击计数器写入到磁盘，并执行其他类似的清理活动。

## 3.职责

1. 创建并返回一个包含基于客户请求性质的动态内容的完整的html页面；
2. 创建可嵌入到现有的html页面中的一部分html页面（html片段）；
3. 读取客户端发来的隐藏数据；
4. 读取客户端发来的显示数据；
5. 与其他服务器资源（包括数据库和java的应用程序）进行通信；
6. 通过状态代码和响应头向客户端发送隐藏数据。