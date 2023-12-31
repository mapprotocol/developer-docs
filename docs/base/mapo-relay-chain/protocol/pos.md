## 股权证明

MAP 中继链（Atlas）是一种权益证明区块链。与比特币和以太坊等工作量证明系统相比，这消除了对环境的负面影响，意味着用户可以进行更便宜、
更快的交易，而且交易结果一旦完成就无法改变。 MAP 中继链实现了伊斯坦布尔拜占庭容错 (IBFT)
共识算法，其中一组明确定义的验证器者节点按一系列步骤在它们之间广播签名消息，以便即使在多达三分之一的节点离线、有缺陷或恶意时也能达成一致。
当法定数量的 validator 达成一致时，该决定即为最终决定。

##  Validator

验证器收集从其他节点收到的交易并执行任何相关的智能合约以形成新的块，然后参与拜占庭容错（BFT）共识协议以推进网络状态。由于
BFT 协议只能扩展到几百个参与者，并且最多可以容忍三分之一的参与者进行恶意行为，因此权益证明机制只允许有限的一组节点担任此角色。

## 质押要求

Atlas 采用权益证明共识机制，要求验证人必须锁定 MAPO 才能参与区块生产。当前的要求是 1,000,000 MAPO。

## 关于选举

在每一个 epoch 产生最后一个区块时会调用选举合约来选择下一个 epoch 的 validator 集合。合约维护每个 validator 的锁定 MAPO 投票（待定或激活）的排序列表。
在每个 epoch 产生最后一个区块期间处理交易和 epoch 奖励后，进行选举来更新活跃的 validator 集合。
可以选择的活跃 validator 的数量有最小目标（1）和最大上限（100）。如果未达到最低目标，则选举将中止，并且不会对该 epoch 的验证器集进行任何更改。

##  Validator 编号和奖励

参与者通过锁定 MAPO 并投票给 validator 来做出这些决定。 每个 epoch 都会进行 validator 选举（大约每三天一次）。该协议最多选举 100
个 validator。在每个新的 epoch 中所有当选的 validator 都必须重新选举才能继续。validator 的选举是根据每个 validator 收到的投票的比例来选择的。
如果您持有 MAPO ，或者是允许投票的 Release MAPO 合约的受益人，您可以投票给 validator。单个帐户可以拆分其锁定的 MAPO 余额，以在 1 个
validator 和 10 个 validator 之间拥有未决投票。一旦社区通过了支持奖励的治理提案，您锁定并用于投票给 validator的 MAPO
就会在每个 epoch 收到奖励。
