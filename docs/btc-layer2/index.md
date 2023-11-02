---
title: 比特币二层
description: 对MAPO作为比特币二层的实现逻辑。
lang: zh
---


愿景是利用比特币 PoW 的安全性来增强PoS区块链的安全性，解决POS的长程攻击问题。

## 比特币二层

### 基于btc的MAPO平台的安全性

比特币以其巨大的算力保护，可以被看作是一个天然信任源，其引入了一种由工作量证明提供支持的时间戳服务器。它为事件提供了不可逆的时间顺序。在其原生应用中，事件是在比特币账本上执行各种交易。在目前增强其他区块链安全性的应用中，比特币也可用于对其他区块链中发生的事件添加时间戳。每个此类事件都会触发发送给矿工的交易，矿工随后将其插入比特币账本，从而为事件添加时间戳。为事件加时间戳的交易称为检查点（checkpoints）。

`检查点`可以通过使用比特币的 `OP_RETURN`  操作码来实现，该操作码允许在不可花费的比特币交易中发布 80 字节的任意数据。每个检查点必须至少包含要检查的 PoS 区块的哈希（32 个字节）以及最终确定该区块的签名（每个 32 字节）。在这里，哈希用于识别被检查点的 PoS 区块。哈希用于识别被检查点的 PoS 区块。需要签名来防止对手发送任意哈希并假装其正在比特币上检查 POS区块。

POS链可以通过比特币时间戳服务的特性，来增强其安全性，解决POS链[长程攻击](https://medium.com/@abhisharm/understanding-proof-of-stake-through-its-flaws-part-3-long-range-attacks-672a3d413501)问题。`MAPO`平台定期（每个epoch）将每个epoch的最后一个区块的哈希及签名提交`检查点`的方式提交到比特币网络，`检查点`由区块的哈希以及单个聚合 BLS 签名‌组成，该签名对应于已签署区块以进行最终确定的 2/3 验证者集的签名以及 epoch 编号和bitmap编号。为此 `MAPO`客户端可以通过检索比特币网络上的`检查点`来判断，`MAPO`平台POS链的最终规范链，从而防范恶意验证者对`MAPO`台实施的[长程攻击](https://medium.com/@abhisharm/understanding-proof-of-stake-through-its-flaws-part-3-long-range-attacks-672a3d413501)。

![架构图](./frame1.png) 


### btc上brc20资产

`MAPO`平台支持将比特币网络上的[铭刻](https://docs.ordinals.com/inscriptions.html)资产（brc20）跨链转移到`MAPO`平台，实现[铭刻](https://docs.ordinals.com/inscriptions.html)资产的价值流动性，有助于扩大比特币的用例和整合到更广泛的加密金融生态系统中。

`MAPO`提供了一套完整的解决方案，使得用户可以非常方便的将brc20资产转移到`MAPO`,`MAPO`使用原生比特币上的[mapo-brc201](/docs/btc-layer2/brc201.md)协议，用户可以无任何损失的将资产从brc20协议转移到brc201协议并跨链到`MAPO`平台。如图所示:

![架构图](./frame2.png) 

+ Indexer服务: 主要用于收集和解析比特币网络上的铭刻交易
+ Collection服务: 根据Indexer服务解析的铭刻交易，分析并处理Brc20和Brc201协议数据并保存相关的用户资产
+ Bridge服务: 将比特币网络上的brc20资产通过brc201协议路由到`MAPO`平台
+ Order服务: 支持构建brc201协议的铭刻交易


