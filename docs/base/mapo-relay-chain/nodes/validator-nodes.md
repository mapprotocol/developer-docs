## 什么是 validator 节点

validator 节点收集从其他节点收到的交易并执行任何相关的智能合约以形成新的区块，然后参与拜占庭容错（BFT）共识协议以推进网络状态。由于
BFT 协议只能扩展到几百个参与者，并且最多可以容忍三分之一的参与者进行恶意行为，因此权益证明机制只允许有限的一组节点担任此角色。

## 运行 validator 节点

要运行一个 validator 节点，您需要有一个注册到 validator 集合中账号。如何注册 validator 请参考 [这里](./validator-registration.md) todo

通过线面的命令运行 validator 节点：
```shell
atlas --datadir ./node --syncmode "full" --port 30321 --v5disc --mine --miner.validator 0x98efa292822eb7b3045c491e8ae4e82b3b1ac005 --unlock 0x98efa292822eb7b3045c491e8ae4e82b3b1ac005
```


