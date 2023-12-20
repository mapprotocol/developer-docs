---
title: 比特币二层
description: 对MAP Protocol作为比特币二层的实现逻辑。
lang: zh
---



## 比特币二层
和以太坊生态系统内的二层类似，比特币二层也是专注于为生态带来更多用例、减少交易费用的网络。常见的比特币二层方案如链下网络、中心化侧链和联邦侧链，都为比特币网络提供了更好的可扩展性和隐私保护，但在比特币生态相比于以太坊而言仍然较为滞后。而在2023年5月，基于 [Ordinals 协议](https://docs.ordinals.com/)、旨在测试 Ordinals 协议是否能够促进比特币的可替代性的的创新实验BRC-20，实现像了在以太坊网络上发行 ERC-20 代币的效果，让社区看到比特币互操作性的可能。

作为点对点、全链互操作基础设施，MAP Protocol 一直专注于跨链互操作。现在，MAP Protocol 还将作为比特币二层，一方面利用比特币网络进一步确保其 MAP Protocol 网络的安全，另一方面则将利用其全链技术，进一步丰富比特币生态，为 BRC-20 社区带来全新交易体验。


### 通过比特币网络进一步确保 MAP Protocol 的安全性

比特币以其巨大的算力保护，可以被看作是一个天然信任源，其引入了一种由工作量证明提供支持的时间戳服务器。它为事件提供了不可逆的时间顺序。在其原生应用中，事件是在比特币账本上执行各种交易。在目前增强其他区块链安全性的应用中，比特币也可用于对其他区块链中发生的事件添加时间戳。每个此类事件都会触发发送给矿工的交易，矿工随后将其插入比特币账本，从而为事件添加时间戳。为事件加时间戳的交易称为检查点（checkpoints）。

`检查点`可以通过使用比特币的 `OP_RETURN`  操作码来实现，该操作码允许在不可花费的比特币交易中发布 80 字节的任意数据。每个检查点必须至少包含要检查的 PoS 区块的哈希（32 个字节）以及最终确定该区块的签名（每个 32 字节）。在这里，哈希用于识别被检查点的 PoS 区块。哈希用于识别被检查点的 PoS 区块。需要签名来防止对手发送任意哈希并假装其正在比特币上检查 POS区块。

POS链可以通过比特币时间戳服务的特性，来增强其安全性，解决POS链[长程攻击](https://medium.com/@abhisharm/understanding-proof-of-stake-through-its-flaws-part-3-long-range-attacks-672a3d413501)问题。`MAPO`平台定期（每个epoch）将每个epoch的最后一个区块的哈希及签名提交`检查点`的方式提交到比特币网络，`检查点`由区块的哈希以及单个聚合 BLS 签名‌组成，该签名对应于已签署区块以进行最终确定的 2/3 验证者集的签名以及 epoch 编号和bitmap编号。为此 `MAPO`客户端可以通过检索比特币网络上的`检查点`来判断，`MAPO`平台POS链的最终规范链，从而防范恶意验证者对`MAPO`台实施的[长程攻击](https://medium.com/@abhisharm/understanding-proof-of-stake-through-its-flaws-part-3-long-range-attacks-672a3d413501)。

![架构图](./frame1.png) 

### 基于比特币网络检查点的MAP中继链

MAP中继链拥有一组独立的验证者集合，用于维护整个中继链的安全性和稳定性，每个验证者都将对每个新产生的区块进行验证并签名，以保证区块的正确性和合法性，满足2/3验证者签名的区块将被最终确认并上链存储。

验证者将对如下数据进行签名:

**hash**: 未包含其他签名信息的区块头hash

**round**: 验证者达成共识的顺序号

**commit**: 常量数据

签名数据`Msg`包含了区块头的`hash`,达成共识的`round`以及一个`MsgCommit`:

```golang
// hash: header.Hash()
func PrepareCommittedSeal(hash common.Hash, round *big.Int) []byte {
	var buf bytes.Buffer
	buf.Write(hash.Bytes())
	buf.Write(round.Bytes())
	buf.Write([]byte{byte(istanbul.MsgCommit)})
	return buf.Bytes()
}
```

验证者对数据进行签名后并搜集其他验证者的签名，将签名数据进行聚合后并以相关数据构建成一个`IstanbulExtra`结构，如下：

```golang
type IstanbulExtra struct {
	// AddedValidators are the validators that have been added in the block
	AddedValidators []common.Address
	// AddedValidatorsPublicKeys are the BLS public keys for the validators added in the block
	AddedValidatorsPublicKeys []blscrypto.SerializedPublicKey
	// AddedValidatorsG1PublicKeys are the BLS public keys for the validators added in the block
	AddedValidatorsG1PublicKeys []blscrypto.SerializedG1PublicKey
	// RemovedValidators is a bitmap having an active bit for each removed validator in the block
	RemovedValidators *big.Int
	// Seal is an ECDSA signature by the proposer
	Seal []byte
	// AggregatedSeal contains the aggregated BLS signature created via IBFT consensus.
	AggregatedSeal IstanbulAggregatedSeal
	// ParentAggregatedSeal contains and aggregated BLS signature for the previous block.
	ParentAggregatedSeal IstanbulAggregatedSeal
}

type IstanbulAggregatedSeal struct {
	// Bitmap is a bitmap having an active bit for each validator that signed this block
	Bitmap *big.Int
	// Signature is an aggregated BLS signature resulting from signatures by each validator that signed this block
	Signature []byte
	// Round is the round in which the signature was created.
	Round *big.Int
}
```

`IstanbulExtra`结构的数据经过rlp编码后存入`header.Extra`字段，其中就包含了达成共识的`round`数据，bls的签名数据`AggregatedSeal`.`Signature`以及变化的验证者。[更多信息](https://docs.mapprotocol.io/develop/map-relay-chain/consensus/aggregatedseal)

#### 检查点

MAP Protocol会定期将中继链上的区块构建成一个`检查点`并提交到比特币网络，以确保整个中继链的确定性，确保不会被伪造而推翻。`检查点`需要包含：

+ PreCheckPointHash: 上一次检查点的哈希
+ Root: 用于确保中继链当时状态的根哈希
+ Height: 检查点对应的中继链上的区块高度

**发布中继链区块哈希到比特币网络**：

+ 生成检查点: 中继链定期生成POS链的检查点信息。构建包含了指定高度的验证者聚合签名的根哈希和确认过时间顺序的前一个检查点哈希。

+ 创建OP_RETURN交易: 中继链将生成的检查点信息,构建为OP_RETURN交易。


**检查点时间顺序**：

+ 中继链将根据其上的`btc-light-client`，确保检查点交易的时间顺序，并在检查校验时保证比特币检查点的时间戳是递增的。可以通过检查新收到的检查点的时间戳是否大于先前接收的检查点的时间戳来实现。如果时间戳递减或者相等，则拒绝该检查点。
  

**校验检查点信息**：

+ 中继链客户端检查比特币交易中的OP_RETURN输出，提取检查点信息，包括哈希、高度等。
+ 根据相应高度的检查点信息来验证中继链本地数据信息是否一致，如中继链通过相应的高度获取对应的验证者集合，验证`检查点`中的哈希的对应的签名是否一致，来判断中继链是否被攻击。

整体结构如下: 

![架构图](./frame3.jpg) 


### 丰富比特币生态，提升 BRC-20 资产的价值流动性

`MAPO`平台支持将比特币网络上的[铭刻](https://docs.ordinals.com/inscriptions.html)资产（brc20）以点对点的方式跨链转移到`MAPO`平台，让其他公链上的币种与 BRC-20 资产以更方便、更低成本的路径实现交易，同时实现[铭刻](https://docs.ordinals.com/inscriptions.html)资产的价值流动性。这一互操作性有助于扩大比特币的用例，将比特币生态整合到更广泛的加密金融生态系统中，从而也比特币社区带来新的贡献。

`MAPO`提供了一套完整的解决方案，使得用户可以非常方便的将brc20资产转移到`MAPO`,`MAPO`使用原生比特币上的[mapo-brc201](./brc201.md)协议，用户可以无任何损失的将资产从brc20协议转移到brc201协议并跨链到`MAPO`平台。如图所示:

![架构图](./frame2.png) 

+ Indexer服务: 主要用于收集和解析比特币网络上的铭刻交易
+ Collection服务: 根据Indexer服务解析的铭刻交易，分析并处理Brc20和Brc201协议数据并保存相关的用户资产
+ Bridge服务: 将比特币网络上的brc20资产通过brc201协议路由到`MAPO`平台
+ Order服务: 支持构建brc201协议的铭刻交易


