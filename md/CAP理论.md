<!--
 * @Author: tangdaoyong
 * @Date: 2021-02-01 10:20:23
 * @LastEditors: tangdaoyong
 * @LastEditTime: 2021-02-01 11:11:31
 * @Description: CAP 定理
-->
# CAP 定理

[Michael Whittaker 图解CAP 定理](https://mwhittaker.github.io/blog/an_illustrated_proof_of_the_cap_theorem/)
[分布式理论(一) —— CAP 定理](https://www.jianshu.com/p/4118718658ac)

## 介绍

1998年，加州大学的计算机科学家 Eric Brewer 提出，分布式系统有三个指标。

* Consistency（一致性）
* Availability（可用性）
* Partition tolerance（分区容错）

Eric Brewer 说，这三个指标不可能同时满足（反正法可以快速证明），这个结论就叫做 CAP 定理。

## Consistency（一致性）

Consistency（一致性），意思在写操作完成之后开始的任何读操作都必须返回该值。

## Availability（可用性）

Availability（可用性），意思是只要收到用户的请求（系统中非故障节点），服务器就必须给出回应。

## Partition tolerance（分区容错）

Partition（分区）：大多数分布式系统都分布在多个子网络，每个子网络就叫做一个区（partition）。造成 `Partition` 的原因可能是网络不可达，也可能是 `GC` 的 `Stop The World` 阻塞太久，也可能是 `CPU` 彪到一个死循环上，总之种种血案。`aphyr` 曾整理过这么一批血案可以参考： [aphyr/partitions-post](https://github.com/aphyr/partitions-post)
Partition tolerance（分区容错），意思是网络将被允许任意丢失从一个节点发送到另一节点的许多消息，分区容错即，我们的系统必须能够在任意网络分区下正常运行。

## 总结

分布式系统中一般是`分区容错`的，所以`一致性`和`可用性`是需要有取舍的。一般是选择`一致性`，那什么情况系统的`可用性`高于`一致性`？
举例来说，发布一张网页到 CDN，多个服务器有这张网页的副本。后来发现一个错误，需要更新网页，这时只能每个服务器都更新一遍。
一般来说，网页的更新不是特别强调一致性。短时期内，一些用户拿到老版本，另一些用户拿到新版本，问题不会特别大。当然，所有人最终都会看到新版本。所以，这个场合就是可用性高于一致性。

