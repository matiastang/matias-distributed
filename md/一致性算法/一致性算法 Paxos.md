<!--
 * @Author: tangdaoyong
 * @Date: 2021-02-01 11:32:33
 * @LastEditors: tangdaoyong
 * @LastEditTime: 2021-02-01 11:54:40
 * @Description: 一致性算法 Paxos
-->
# 一致性算法 Paxos

[分布式系统Paxos算法](https://www.jdon.com/artichect/paxos.html)
[一致性算法raft和paxos](https://zhuanlan.zhihu.com/p/60175456)
[分布式理论(五)—— 一致性算法 Paxos](https://www.cnblogs.com/stateis0/p/9062130.html)
## 介绍

拜占庭将军问题简化：假设将军中没有叛军，信使的信息可靠但有可能被暗杀的情况下，将军们如何达成一致性决定？

Paxos算法是第一个被证明的共识算法，但难于理解。Raft算法具有相同的运行效率，但更容易理解，更容易被用于系统开发。

## 总结

Paxos 协议/算法 就是少数服从多数，标准的 鸽笼原理/抽屉原理，同时，还会根据议员的身份来判断是否需要应答，这个身份其实就是一个编号，是为了防止出现活性导致死循环。

注意：这一切都是在没有 拜占庭将军 问题的基础上建立的，即消息不会被篡改（因为分布式大多在局域网中）。

Paxos 的目标：保证最终有一个提案会被选定，当提案被选定后，其他议员最终也能获取到被选定的提案。

Paxos 协议用来解决的问题可以用一句话来简化： 将所有节点都写入同一个值，且被写入后不再更改。

引用
[如何浅显易懂地解说 Paxos 的算法？](https://www.zhihu.com/question/19787937)
[图解 Paxos 一致性协议](http://blog.xiaohansong.com/2016/09/30/Paxos/)
[分布式系列文章——Paxos算法原理与推导](http://www.cnblogs.com/linbingdong/p/6253479.html)
[Paxos算法 维基百科，自由的百科全书](https://zh.wikipedia.org/wiki/Paxos%E7%AE%97%E6%B3%95)
[Paxos Made Simple【翻译】](https://wenku.baidu.com/view/bf8e118fa0116c175f0e4853.html?from=search)
[The Part-Time Parliament(Paxos算法中文翻译)](https://wenku.baidu.com/view/87276e1dfad6195f312ba6d7.html)