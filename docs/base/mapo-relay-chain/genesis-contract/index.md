---
title: Atlas创世合约
description: 
lang: zh
---


`Atlas`的创世合约是一组智能合约,包含了多个关键的功能合约，它们相互协作以支持`Atlas`网络的运行。

+ Accounts（账户合约）：Accounts合约用于管理`Atlas`网络上的 validator 和 voter 的账户。主要包含用户创建新账户、设置和查看账户信息、账号授权和验证。Accounts合约与Election合约和LockedGold合约相关联，用户可以使用锁仓的MAPO代币来参与验证者选举并进行账户之间的转账。

+ Election（选举合约）：Election合约负责管理`Atlas`网络中的验证者选举。它定义了验证者的选举算法和条件，管理维护各种状态的投票信息。Election合约使用LockedGold合约中锁仓的MAPO代币作为验证者的抵押品。验证者通过参与验证和打包交易来维护网络的安全性和稳定性。

+ EpochRewards（每个EPOCH奖励合约）：EpochRewards合约负责管理和分发奖励给参与`Atlas`网络的验证者和其他网络参与者。它根据验证者的贡献和网络的安全表现，将一定数量的MAPO代币作为奖励分发给合格的验证者。EpochRewards合约与Validators合约和LockedGold合约密切相关，验证者需要在Validators合约中注册才能参与奖励分发。

+ Validators（验证者合约）：Validators合约维护和管理`Atlas`网络中的验证者节点。它定义了验证者的角色和职责，并提供了必要的功能和接口，以使验证者能够验证和打包交易，参与共识过程。Validators合约与Election合约一起工作，以确定哪些验证者有资格参与区块的验证和生成。Validators合约还与LockedGold合约相关，验证者需要在LockedGold合约中锁仓一定数量的MAPO代币作为抵押。

+ LockedGold（锁仓合约）：LockedGold合约用于管理`Atlas`网络上的锁仓资产。用户可以将自己的MAPO代币锁定在合约中作为验证者的抵押物或参与网络治理。锁仓资产的抵押可以帮助确保网络的安全，并参与到`Atlas`的决策投票中。LockedGold合约与Election合约和Validators合约直接相关，因为验证者需要在LockedGold合约中锁仓一定数量的MAPO代币，并使用这些锁仓资产参与验证者选举和验证过程。

这些创世合约之间存在紧密联系，它们共同构成了`Atlas`网络的核心功能。共同促进了`Atlas`网络的安全性、稳定性和去中心化的金融生态系统的发展。