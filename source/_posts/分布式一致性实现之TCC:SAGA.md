---
title: "分布式一致性实现之TCC/SAGA"
catalog: true
date: 2019-07-24 18:50:00
subtitle: "This is about 2PC/3PC"
header-img: "Demo.png"
tags:
- 一致性协议
- TCC/SAGA
catagories:
- 事务一致性


---

## 一.概述

之前提到了一致性协议中的2PC/3PC，可以在一定程度上解决分布式事务的问题，但是在微服务架构中依然不合适，原因如下：

- 由于微服务间无法直接进行数据访问，微服务间互相调用通常通过RPC（dubbo）或Http API（SpringCloud）进行，所以已经无法使用TM统一管理微服务的RM。
- 不同的微服务使用的数据源类型可能完全不同，如果微服务使用了NoSQL之类不支持事务的数据库，则事务根本无从谈起。
- 即使微服务使用的数据源都支持事务，那么如果使用一个大事务将许多微服务的事务管理起来，这个大事务维持的时间，将比本地事务长几个数量级。如此长时间的事务及跨服务的事务，将为产生很多锁及数据不可用，严重影响系统性能。



## 二.CAP理论和BASE理论

### 1.CAP

1998年，加州大学的计算机科学家 Eric Brewer 提出，分布式系统有三个指标。

- Consistency
- Availability
- Partition tolerance

它们的第一个字母分别是 C、A、P。Eric Brewer 说，这三个指标不可能同时做到。这个结论就叫做 CAP 定理。

### 2.BASE

BASE理论由eBay的架构师Dan Pritchett提出，BASE理论是对CAP理论的延伸，核心思想是即使无法做到强一致性，应用应该可以采用合适的方式达到最终一致性。BASE是指：

- Basically Available
- Soft State
- Eventual Consistency



## 二.TCC

### 1.概述

关于 TCC（Try-Confirm-Cancel）的概念，最早是由 Pat Helland 于 2007 年发表的一篇名为《Life beyond Distributed Transactions：an Apostate’s Opinion》的论文提出。 

对于 TCC 的解释：

- **Try 阶段：**尝试执行，完成所有业务检查（一致性），预留必需业务资源（准隔离性）。
- **Confirm 阶段：**确认真正执行业务，不作任何业务检查，只使用 Try 阶段预留的业务资源，Confirm 操作满足**幂等性**。要求具备幂等设计，Confirm 失败后需要进行重试。
- **Cancel 阶段：**取消执行，释放 Try 阶段预留的业务资源，Cancel 操作满足幂等性。Cancel 阶段的异常和 Confirm 阶段异常处理方案基本上一致。



## 三.SAGA

### 1.概述

Saga 是 30 年前一篇数据库伦理提到的一个概念。其核心思想是将长事务拆分为多个本地短事务，由 Saga 事务协调器协调，如果正常结束那就正常完成，如果某个步骤失败，则根据相反顺序一次调用补偿操作。

**Saga 的组成：**每个 Saga 由一系列 sub-transaction Ti 组成，每个 Ti 都有对应的补偿动作 Ci，补偿动作用于撤销 Ti 造成的结果。这里的每个 T，都是一个本地事务。

可以看到，和 TCC 相比，Saga 没有“预留 try”动作，它的 Ti 就是直接提交到库。



### 2.执行顺序

Saga 的执行顺序有两种：

- **T1，T2，T3，...，Tn。**
- **T1，T2，...，Tj，Cj，...，C2，C1，其中 0 < j < n 。**



### 3.恢复策略

Saga 定义了两种恢复策略：

- **向后恢复，**即上面提到的第二种执行顺序，其中 j 是发生错误的 sub-transaction，这种做法的效果是撤销掉之前所有成功的 sub-transation，使得整个 Saga 的执行结果撤销。
- **向前恢复，**适用于必须要成功的场景，执行顺序是类似于这样的：T1，T2，...，Tj(失败)，Tj(重试)，...，Tn，其中 j 是发生错误的 sub-transaction。该情况下不需要 Ci。



这里要注意的是，在 Saga 模式中不能保证隔离性，因为没有锁住资源，其他事务依然可以覆盖或者影响当前事务。

