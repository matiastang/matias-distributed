<!--
 * @Author: tangdaoyong
 * @Date: 2021-02-04 10:10:19
 * @LastEditors: tangdaoyong
 * @LastEditTime: 2021-02-04 10:19:44
 * @Description: COPS算法
-->
# COPS算法

[分布式系统因果一致性与COPS算法](https://www.jdon.com/artichect/cops.html)

COPS是保序系统的集群(Clusters of Order Preserving System)的简称。在了解COPS之前，最好首先了解一下分布式系统CAC算法，从[CAP]()-->[CAC](https://www.jdon.com/artichect/consistency-availability-and-convergence.html)-->[COPS](https://www.jdon.com/artichect/cops.html)是分布式系统的确保高性能低延迟高可扩展性的前提下追求高一致性直至事务机制的发展路径。

 COPS系统能够提供因果+一致性causal+ consistency，设计为支持复杂的在线应用，这些应用托管在少量的大规模数据中心，每个应用都是有前端服务器(COPS客户端)以及后端key-value数据存储，COPS在本地数据中心以线性化方式执行所有的put写操作和get读操作，然后会跨数据中心以因果一致性的顺序在后台进行复制。

　　通过COPS我们能够获得ALPS：Availability, Low-Latency,Partition-tolerance, 和 Scalability.

可用性 Availability: 所有操作读能成功，没有会无限期堵塞或返回一个显示数据不可用的错误。
低延迟Low-latency: 目标响应时间在毫秒级别。
分区容错Partition tolerance: 数据存储能够在网络分区的情况下持续操作。
可扩展性Scalability: 数据存储能够线性水平扩展。
　　按照CAP定理，这种ALPS系统必然会牺牲强一致性，比如linearizability线性一致性，但是我们还是能够在此约束下寻找到最强的一致性，也就是因果一致性。

　　下面看看什么是因果一致性。我们假设一个key-value数据存储，有两个基本操作: put(key,val) 和 get(key)=val. 这个类似于在单机的共享内存系统中的读操作和写操作(可参考：[Go语言Goroutine与Channel内存模型](http://www.jdon.com/concurrent/golang-memory.html))。

　　我们约定遵循下面三个规则表示潜在一致性，用符号表达 ->：

在同一执行线程：. 如果a 和 b 是一个执行线程中的两个操作，如果操作a发生在操作b之前，那么a ->b；
不同线程Gets From. 如果 a是一个put放入操作，且b是一个获得操作，能返回被a放入的写操作结果值，那么a->b；
传递性Transitivity. 对于操作a, b, 和 c, if a -> b 且 b -> c, 那么 a -> c.
 

　　这些规则在同一个线程内的操作之间以及在与数据存储交互的不同线程的操作之间创建了潜在的因果关系，这个模型，并不允许线程直接通讯，而是通过数据存储进行通讯。