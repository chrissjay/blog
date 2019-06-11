---
title: "[Hexo] Shiro探究"
catalog: true
date: 2019-06-01 20:41:00
subtitle: "This is about Shiro."
header-img: "Demo.png"
tags:
- Shiro
- Spring Boot
catagories:
- Shiro
---

之前制作网站的时候使用了Apache Shiro进行登录验证/权限认证，相比Spring Security，它更小巧，更容易上手，而且和Spring Boot的配合使用也完全不麻烦。

		
**Shiro的四大功能**
 1. Authentication：有时也简称为“登录”，这是一个证明用户是他们所说的他们是谁的行为。
 2. Authorization：访问控制的过程，也就是绝对“谁”去访问“什么”。
 3. Session Management：管理用户特定的会话，即使在非 Web 或 EJB 应用程序。
 4. Cryptography：通过使用加密算法保持数据安全同时易于使用。

**Shiro的架构的3个主要的概念**
Subject，SecurityManager 和 Realms

我对这些的理解比较通俗。

Subject就是对象，比如登录的用户。

Realms里面应该定义登录/权限的逻辑，因为框架并不知道你的逻辑是怎么样的，所以往往需要重写Realm方法。然后它负责对数据库的一些操作，可以看作DAO层。

而SecurityManager相当于管理Realms的容器，它负责管理Reamls，以及各种内部组件。（只是从这个方面看的话，感觉和分布式框架中的注册中心zookeeper/eureka的作用类似。当然此外这两者是完全不同的。）

**ShiroConfiguration**
因为我使用的是Spring boot，所以相比Spring配置更为简便。而需要集成Shiro的话，需要单独配置ShiroConfiguration。
步骤也很简单，只有三步：

 1. 配置Realms，可以配置一个/多个，也可以选择重写。我个人是重写了授权和鉴权的过程。
 2. 调用SecurityManager注册Realm，类似于将服务提交给注册中心
 3. 配置过滤器

过滤器不需多说，要想避免直接访问网站的静态资源必备，而且可以根据权限来决定可以访问的页面。其配置方法有许多种，我个人采用的是比较简单粗暴的链式过滤。就是使用了LinkedHashMap（这个是有序的哈希表），按照顺序匹配。匹配成功则提供页面，不成功则跳转到指定页面。

这是比较基本的用法。但是实际应用中，每次访问页面都要进行一次鉴权会比较麻烦，所以我在ShiroConfiguration里集成了Redis缓存，这里便不详细说明了。

其实Shiro作为一个企业级的框架，功能非常强大。在这里登录和权限验证算是一个比较基础的功能，期待以后可以继续发掘它以及一直想玩的Spring Security。
