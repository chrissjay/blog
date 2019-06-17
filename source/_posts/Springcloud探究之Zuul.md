---
title: "Springcloud探究之Zuul"
catalog: true
date: 2019-06-01 21:44:00
subtitle: "This is about Spring cloud."
header-img: "Demo.png"
tags:
- Spring cloud
- Zuul
catagories:
- Spring cloud
---

# Zuul

Zuul是Springcloud组件中比较重要的一个组件，起到了一个网关的作用（相似作用的还有GateWay，但是还没研究过，等研究了之后可以写一篇对比）



## 主要功能

设想，在一个分布式的架构中，许多服务会根据不同的功能划分为不同的微服务模块，而我们客户端如果直接能够请求到服务，那么从安全角度和代码重构角度而言都不是一个很好的选择。此外，不同终端对于服务的请求机制也不尽相同，例如手机app目前不会和网页端一样发送类似REST风格的请求等等。因此引入网关作为中间层很有必要。



### 架构

![img](http://www.xdlysk.com/wp-content/uploads/2018/02/gateway.png)

![img](https://img.mukewang.com/5b4f1bfd000113fa12000644.jpg)

**简而言之，客户端想要请求到服务，首先请求发送zuul网关，然后zuul网管负责请求分发到对应的服务去。**



### 增强特性

除了转发之外，还可以结合鉴权，负载均衡等功能

