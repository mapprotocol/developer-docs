## 什么是 validator 节点

validator 节点收集从其他节点收到的交易并执行任何相关的智能合约以形成新的区块，然后参与拜占庭容错（BFT）共识协议以推进网络状态。由于
BFT 协议只能扩展到几百个参与者，并且最多可以容忍三分之一的参与者进行恶意行为，因此权益证明机制只允许有限的一组节点担任此角色。

## 质押

Atlas 采用权益证明共识机制，如果想参与 atlas 网络的区块生成需要注册成 validator。目前想要成为一个 validator 需要锁定
1,000,000 个 MAPO。

## 运行 validator 节点

要运行一个 validator 节点，您需要有一个注册到 validator 集合中的账号。如何注册 validator
请参考 [这里](/docs/base/mapo-relay-chain/example/how-to-become-a-new-validator.md)

通过下面的命令运行 validator 节点：

```shell
atlas --datadir ./node --syncmode "full" --port 30321 --v5disc --mine --miner.validator 0x98efa292822eb7b3045c491e8ae4e82b3b1ac005 --unlock 0x98efa292822eb7b3045c491e8ae4e82b3b1ac005 console
```

## 相关主题

- [选举](/docs/base/mapo-relay-chain/protocol/election.md)
- [奖励](/docs/base/mapo-relay-chain/protocol/rewards.md)
- [如何成为一个 Validator](/docs/base/mapo-relay-chain/example/how-to-become-a-new-validator.md)