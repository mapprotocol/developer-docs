介绍 Atlas 链 validator 选举以及投票的管理。

## 质押

Atlas 采用权益证明共识机制，如果想参与 atlas 网络的区块生成需要注册成 validator。目前想要成为一个 validator
需要[锁定](/docs/base/mapo-relay-chain/marker/common.md#lockedmap)
1,000,000 个 MAPO。并对其进行[投票](/docs/base/mapo-relay-chain/marker/vote.md#vote)。每次选举会按照 validator 收到的票数排序，选出前
N 名 validator。

## 更新活动验证者集

在处理交易和 Epoch 奖励之后，通过在每个 epoch 的最后一个区块中运行选举来更新活动验证者集。

## 选举验证者

验证者必须至少拥有总票数的 0.001 比例才能考虑参加选举。所以验证者不能没有选票。
这样做的好处是避免烧毁 MAPO 并将投票人数限制在1000人以内。
可选择的活跃验证器数量有最小目标 (1) 和最大上限 (100)。如果未达到最低目标，则选举将中止，并且不会对该 epoch 的验证器集进行任何更改。
示例：现在链上有四个验证者，他们是：

- 0x5d643dfb9ae372ce4fdbc80890156e2cd8290846
- 0xa53516d49a72019692ac69cb42641942597654f6
- 0x6acdc02223100189d82a958d888f54fa27d60e8a
- 0xea9efaa232a4567eac21c8c096f8bff84595a244

如果由于某些原因我们不选举验证者（有效验证者数量小于1），我们将继续使用上述验证者。如果我们选择最新的一组验证者（这意味着新验证者的数量大于
1 且小于 100），我们将用新验证者替换上述验证者。

## 解除质押

在质押(锁定)成功后如果你有需求可以解除质押(解锁)，解除质押 15
天后你可以通过[赎回](/docs/base/mapo-relay-chain/example/how-to-withdraw.md)操作将 MAPO 赎回到你的账户余额。

## 执行

[Election](https://github.com/mapprotocol/atlas-contracts/blob/main/contracts/governance/Election.sol) 合约管理锁定 MAPO
投票和纪元奖励并运行验证者选举。

## 相关主题

- [奖励](/docs/base/mapo-relay-chain/protocol/rewards.md)