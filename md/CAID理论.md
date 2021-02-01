<!--
 * @Author: tangdaoyong
 * @Date: 2021-02-01 14:59:04
 * @LastEditors: tangdaoyong
 * @LastEditTime: 2021-02-01 15:00:08
 * @Description: CAID理论
-->
# CAID理论

`CAID`是事务的基础理论，所有事务都必须满足以下特性:

* 原子性（`Atomicity`）:一个事务要么全部执行，要么不执行
* 一致性(`Consistency`) :事务的运行并不改变数据库中数据的一致性
* 隔离性（`Isolation`）:事务的隔离性也称作独立性,是指两个以上的事务不会出现交错执行的状态。因为这样可能会导致数据不一致。
* 持久性(`Durtability`) :事务执行成功以后，该事务对数据库所作的更改便是持久的保存在数据库之中，不会无缘无故的回滚。