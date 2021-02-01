<!--
 * @Author: tangdaoyong
 * @Date: 2021-02-01 12:07:35
 * @LastEditors: tangdaoyong
 * @LastEditTime: 2021-02-01 12:07:49
 * @Description: zooKeeper
-->
# zooKeeper

Kubernetes 将 etcd 作为核心元数据的存储服务，是它的一个高光时刻。随着云原生和容器化的浪潮，越来越多公司基于 Kubernetes 构建上层平台，etcd 是你必会的关键组件。

比ZooKeeper好用的新秀

解决分布式系统数据一致性问题，ZooKeeper 已经稳固了江湖地位，然而 etcd 相比它，有了许多便捷性优势，比如：

支持增量快照，避免了 ZooKeeper 的快照暂停问题；
无需像 ZooKeeper 那样为每个 watch 都做个 socket 连接，可以复用；
ZooKeeper 每个 watch 只能收到一次事件通知，etcd 可以持续监控，一次 watch 触发后无需再设置一次 watch；