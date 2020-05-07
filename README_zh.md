# Chimera 项目白皮书 [English Version](README.md)

## 目标
Chimera 是一条新的公链，它的特点是：

1. 支持秒级 finality。
2. 容易其他公链做双向锚定，使用 light client 的方式

这可以解锁很多有想象力的场景。例如，ETH 上的 USDT 等稳定币资产，将可以被 trustless 地移动到 Chimera 上。因此人们可以通过 Chimera 支付稳定币，数秒后即可到账。

## 技术

### 共识
Chimera 会采取一种混合共识：POW + 类Avalanche。

区块链的共识主要可以分为两大类：

1. Nakamoto Consensus。朴素的POW hash 计算 + 最长链作为 fork choice。极其鲁棒，较为安全，容易理解，too simple to be wrong。这类协议一个较少被人提及的好处是，对于 light client 比较友好。
2. BFT。多个节点的validator集体为新块背书。这类共识包括几十个节点的 dPOS，也包括节点数量略多的 Casper POS。这类协议可能更中心化，有更多的安全风险，如 long range attack 和 nothing at stake。这类协议有一个被较少提及的缺陷，就是 liveness：当1/3节点故障，整个网络可能会停止运行。

我们比较认同 [Nervos 基金会对于 Nakamoto Consensus 的推崇](https://medium.com/nervosnetwork/why-we-love-nakamoto-consensus-5467c035fc55)。我们认为 POW 非常适合作为 Chimera 的“托底”共识。对于不需要 fast finality 的 full nodes（例如一些交易所节点），他们可以仅使用 POW 共识过的块作为“Single Source of Truth”。在这些节点的视角，Chimera 的共识就蜕化成为普通的 Nakamoto Consensus。


Chimera 的第二套共识会采取类似 Avalanche 的快速 finality 共识。严格地说，由于 Chimera 不采用 DAG 的拓扑结构，最多只有一些 uncle blocks，因此一定不是狭义的 [论文中的 Avalanche](https://ipfs.io/ipfs/QmUy4jh5mGNZvLkjies1RWM4YuvJh5o2FYopNPVYwrRVGV)，而更接近 [适用于区块链的 snowman 共识](https://medium.com/avalabs/the-ava-platform-a-tech-primer-7a9b5de57a35) 。不过为了行文方便，下文直接使用 “ Avalanche” 这个词来指代这类 **概率性的快速 finality 协议**。


两套共识如何协作会是一个关键点。一个朴素想法是，协议要求造块节点必须支持 Avalanche 共识，每个在 Nakamoto Consensus 中被确认的块，必须首先被 Avalanche 确认过。每个块的 mint reward，按照一定比例在参与这两种共识的节点中分享。这个朴素想法会是我们未来不断改进设计的起点。


Chimera 的节点可以选择动态地加入或者退出 Avalanche。
为了防止 Sybil attack，考虑结合 POW 或者 [Coin Age](https://github.com/tyler-smith/snowglobe/blob/master/spec/snowglobe.md#sybil-resistance-via-coin-age) 来保证 Avalanche 的安全性。

混合共识需要更多的设计和检查，来保证正确的激励和安全性。

和我们这里的想法类似，BCH 项目有一些对于 POW + Avalanche 的[非常前期的探索和思考](https://github.com/tyler-smith/snowglobe/blob/master/spec/snowglobe.md)。

### 编程模型
Chimera 会和大多数近期的链类似，采用 WASM 作为 VM。 

Chimera 的 VM 中，可以使用 merkle proof / POW proof 来验证 EVM 链上交易中的 “Event” 的有效性。类似的，Chimera 链上的交易产生的 Event 也会体现在 block header hash 的计算中，因此 ETH 上的 Chimera light client 无需执行 WASM ，就可以验证 Chimera Event 的有效性。

这方面一个类似的例子是 [Near 的 Rainbow Bridge](https://github.com/near/rainbow-bridge)。

## 悬而未决的需要进一步思考的技术问题

### Replay
长期双向 relay 的运营费用和 GAS 费用谁来支付？
是否要允许 relay 长期中断之后，仅仅通过数十个块来验证区块的有效性？如果允许，此时 POW 的难度如何验证？

### 升级与分叉
当锚定链 fork 时，Chimera 上的 light client 技术层面和治理层面如何升级？

### 容错
Light client 如果由于 bug 等原因出错，如何回退？

## 融资与项目计划
寻求 100,000 - 150,000 USD 的融资，转让 15-25% 的份额。

预计使用这些投资和6个月左右时间，会搭建一个3-4人的团队，来完成架构的设计和 prototype 的开发。如果技术上顺利，这些投资的一部分可能会用来做 marketing。prototype 基本成形之后，会考虑下一轮融资和扩大团队，或者考虑整体卖给愿意进一步开发和运营的公司。如果技术上发现不可行，剩余的资金将会按比例退还投资人。



### 风险
目前还不能确定技术思路是完全可行的。历史上，区块链项目也不乏因为技术原因失败或者未达到预期的先例，例如 Perlin 和 Plasma。预计需要2-3个月时间想清楚项目的主要技术风险点，判断是不是一定能做。不过前两三个月只需要很少的人，即使判断不太能做，消耗的工资成本基本在 30,000 USD 以内。



