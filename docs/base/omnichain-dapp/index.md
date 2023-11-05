---
title: 全链去中心化应用
description: 基于 MAP Protocol 全链去中心化应用的可能形式.
lang: zh
---

# 全链去中心化应用
全链去中心化应用是指能够以不依赖任何特权第三方、真正去中心化的方式实现跨链的应用。你可以利用[脉波(MAP Protocol)]((docs/base/intro-to-mapo/index.md)) 这一点对点互操作的全链基础设施，开发你的全链去中心化应用。脉波在这个过程中中主要负责跨链消息的传递，协议中核心业务逻辑例如资产管理、质押、铸造、销毁等都是由全链 dApp 自主完成并持续维护。
## MAP Protocol 去中心化跨链流程示意
以下为全链 dApp 实现去中心化跨链的流程示意图，展示了与dApp 逻辑活动相关的交易从Ethereum 经过 MAP Relay Chain 传递到 Polygon。
![](OmniApp.png)

### 具体流程
1. 用户与在 Ethereum dApp 逻辑合约中发生交互
2. 在相应相关逻辑完成后，该合约会去调用MOS合约中的 **`TransferOut`** 方法
3. **`TransferOut`** 方法会 Emit 出相应的 `Event`，该 Event 中包含了逻辑合约中交易的 `calldata`
4. Ethereum-MAPO Messenger 会监听到这个 `Event`
5. Ethereum-MAPO Messenger 会构建该 `Event` 所在交易的证明数据
6. Ethereum-MAPO Messenger 会将该证明数据通过调用 MAP Relay Chain 上 MOS合约 的 **`TransferIn`** 方法传递至 MAP Relay Chain
7. **`TransferIn`** 方法会去部署在 MAP Relay Chain 上的 Ethereum 的轻客户端中验证该证明数据
8. 如果验证成功，会 Emit 出相应的  `Event`；其内容也包含了 Messenger 所传递的 Event 中的相同 `calldata`
9. MAPO-Polygon Messenger 会监听到这个 `Event`
10. MAPO-Polygon Messenger会构建该 `Event` 所在交易的证明数据
11. MAPO-Polygon Messenger会将该证明数据通过调用 Polygon 上** MOS 合约**的  **`TransferIn`** 方法传递至 Polygon
12. T **`TransferIn`** 方法会去部署在 Polygon上的 MAP Relay Chain的轻客户端中验证该证明数据
13. 如果验证成功，MOS 合约会去调用 Polygon上全链 dApp 的逻辑合约并执行所传递的 `calldata`
14. 全链 dApp 的逻辑合约可以 Emi t出‘执行完成’类似的`Event`
## 脉波全链去中心化 dApps 关键技术要件
### [MOS 合约 (MOS Contract)](https://github.com/mapprotocol/mapo-service-contracts/blob/main/evm/contracts/MapoServiceV3.sol)
MAP Omnichain Service (MOS) Contract 是 MAP Protocol 负责跨链消息传递的核心合约。在源链、MAP Relay Chain，以及目标链都会部署相应的 MOS Contract用来**发送、承接以及接受跨链消息**，其中全链 dApp 会涉及到两个关键方法：\
**`TransferOut`**: transferOut 方法会由全链 dApp的逻辑合约 调用并将其内部方法所构建的calldata进行传递。
 ```
    function transferOut(uint256 _toChain, bytes memory _messageData, address _feeToken)
 ``` 
- `uint256 _toChain` 是所要传递的目标链 **chain id**
- `bytes memory _messageData` 是要传递的 **calldata**
- `address _feeToken` 则是所要收取的手续费 **token地址**

**`TransferIn`**: transferIn方法会由Messenger调用并将其所构建的交易相关的证明传递给目标链；transferIn方法还会将证明传递给所在链的轻客户进行验证并再验证成功后执行其所包含的calldata；
 ```
    function transferIn(uint256 _chainId, bytes memory _receiptProof)
 ``` 
- `uint256 _chainId` 是所要MOS所在的链的chain id
- `bytes memory _receiptProof` 是要由Messenger所构建的交易的证明calldata

### [Messenger](https://github.com/mapprotocol/compass)
> Messenger是MAP Protocol负责跨链消息传递的无特权的链间程序。它的主要职责：
- 监听MOS的transfer out交易并构建其在源链的相应证明数据；
- 调用MOS的TransferIn方法来完成跨链的证明数据以及其包含的跨链消息的传递；

### [MAPO Executor](https://github.com/mapprotocol/mapo-service-contracts/blob/main/evm/contracts/interface/IMapoExecutor.sol)
> MAPO Executor 是一个需要开发者自己实现的interface，可以让MOS合约在目标链调用时执行全链Dapp的具体逻辑
```
function mapoExecute (uint256 _fromChain, uint256 _toChain, bytes calldata _fromAddress, bytes32 _orderId, bytes calldata _message）
```
- `uint256 _fromChain` 是起始链的 **chain id**
- `uint256 _toChain` 是目标链的 **chain id**
- `bytes calldata _fromAddress` 是这个交易的发起地址也就是全链dApp的地址
- `bytes _orderId` 则是这笔跨链交易的唯一ID
- `bytes calldata _message` 是这个交易所包含的执行逻辑

## OmniApp 的可能形式
> 通过 MAP Protocol 全链互操作基础设施，开发者可以设计和开发出许多有创意、并具有实际意义的Omni-DApps，我们在这里列举一些可能性。
### Omni-DeFi
全链 DeFi 协议指的是借助 MAP Protocol 的底层基础设施，可以接受来自不同链上的不同资产来参与经济活动的协议。
### Omni-Swap
Omni-Swap是意在通过在不同的链上所创建流动性池加上MAP Protocol 无特权角色的跨链消息传递，使得用户可以轻松完成不同链上的资产兑换；
### Omni-Loan
Omni-Loan是指一种可以在用一条链的资产做抵押并在另一条链借贷出资产的协议。这样做可以使得用户的单链资产在不跨链转移的情况下可以轻易的参与不同链间的经济活动。
### Omni-Staking
Omni-Staking是指一种可以在用一条链的资产在不进行资产跨链兑换的情况下去参与到另一条链的 staking pool 的质押活动中获得收益。
### Omni-NFT
全链NFT是一种可以在链间流转，并且始终保持全链唯一性的NFT，在不同的区块链间始终维持一套统一的tokenID序列。
### Omni-PFP
Omni-PFP是一种在可以在全链自由展示并且随意流转的唯一Profile for Picture NFT，用户可以在A链Burn掉他的NFT并选择在任意一条其他的链上铸造一个一样的tokenID的NFT，这些tokenID在全链范围内都是唯一的。
### Omni-DID
Omni-DID是一个全链的ID/域名系统，允许用户的Omni-DID在全链注册并可被识别；用户在Ethereum上的Omni-DID注册合约中注册BNB Chain的DID关联地址后，BNB Chain的用户就可以通过Omni-DID给该用户转账或者类似的动作
