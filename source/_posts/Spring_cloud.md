---
title: "Spring cloud微服务初探"
catalog: true
date: 2019-06-01 21:44:00
subtitle: "This is about Spring cloud."
header-img: "Demo.png"
tags:
- Spring cloud
- Spring Boot
catagories:
- Spring cloud
---

**很早以前就一直想搭建一个分布式微服务的工程，但是因为种种原因搁置了，这次终于上手了Spring cloud，至于为什么选择Spring cloud而不是dubbo，仅仅是因为Spring cloud基于Spring boot，更加方便，待我学会Spring cloud以后再去探究dubbo**



## Spring cloud简介

Springcloud 是一个优秀的微服务框架，那么什么是微服务架构呢，简单来说就是将一个庞大的系统根据各个服务拆分成不同的模块，降低了耦合度，同时也降低了宕机的损害。它通过服务注册中心管理，方便对各个服务的调度/排查/增删等等。



## Spring cloud主要组件

将Spring cloud称作全家桶一点也不过分，不过作为初学者，我目前仅仅接触了最基本的几个组件：Eureka, Feign等

### 注册中心Eureka

Eureka 是微服务架构中的注册中心，专门负责服务的注册与发现。（类似于常和dubbo配套使用的zookeeper）

### Feign

Feign 是一个声明web服务客户端，这便得编写web服务客户端更容易，使用Feign 创建一个接口并对它进行注解，它具有可插拔的注解支持包括Feign注解与JAX-RS注解（其实对这个我也不太理解，不过它是用到了REST API）

### 待续...



## 简单架构

既然有注册中心，那么肯定需要一个提供服务的端和一个客户端（云端客户端）

另一种更通俗的叫法就是：一个生产者，一个消费者，然后共同由注册中心管理。







