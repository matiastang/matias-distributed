<!--
 * @Author: tangdaoyong
 * @Date: 2021-02-01 14:52:37
 * @LastEditors: tangdaoyong
 * @LastEditTime: 2021-02-01 14:55:09
 * @Description: NWR协议
-->
# NWR协议

NWR 是一种在分布式存储系统中用于控制一致性级别的一种策略。在 Amazon 的 Dynamo 云存储系统中，就应用 NWR 来控制一致性。

让我们先来看看这三个字母的含义：

* N：在分布式存储系统中，有多少份备份数据
* W：代表一次成功的更新操作要求至少有 w 份数据写入成功
* R： 代表一次成功的读数据操作要求至少有R份数据成功读取
NWR值的不同组合会产生不同的一致性效果，当 W+R>N 的时候，整个系统对于客户端来讲能保证强一致性。当 W+R 以常见的 N=3、W=2、R=2 为例：
N=3，表示，任何一个对象都必须有三个副本（Replica），W=2 表示，对数据的修改操作（Write）只需要在 3 个 Replica 中的 2 个上面完成就返回，R=2 表示，从三个对象中要读取到2个数据对象，才能返回。
在分布式系统中，数据的单点是不允许存在的。即线上正常存在的 Replica数量是 1 的情况是非常危险的，因为一旦这个 Replica 再次错误，就 可能发生数据的永久性错误。假如我们把 N 设置成为 2，那么，只要有一个存储节点发生损坏，就会有单点的存在。所以 N 必须大于 2。N约高，系统的维护和整体 成本就越高。工业界通常把 N 设置为3。
当 W 是 2、R 是 2 的时候，W+R>N，这种情况对于客户端就是强一致性的。

在具体实现系统时，仅仅依靠 NWR 协议还不能完成一致性保证，因为在上述过程中，当读取到多个备份数据时，需要判断哪些数据是最新的，如何判断数据的新旧？这需要向量时钟来配合，所以对于 Dynamo 来说，是通过NWR协议结合向量时钟来共同完成一致性保证的。