---
title: mapo protocol实现跨链互通
description: 
lang: zh
---

链间跨链互通是指不同区块链网络之间的数据和资产传输，以实现去中心化应用程序的协作和互操作性。Mapo Protocol通过其独有的`应用层`，`mos层`，`协议层`三层架构可以很方便的接入第三方链和dapp应用开发者；根据Mapo Protocol标准化的模块，第三方链只需要根据自身的共识机制实现自己的轻客户端(solidity版)及部署mapo-relay-chain的轻客户端就可以很方便的实现与mapo-relay-chain的跨链通讯。

Mapo Protocol协议支持连接EVM兼容链和非EVM兼容链，以及支持全链的dapp开发：

+ [链接EVM兼容链](/docs/mapo-stack/chains-connect/evm-chain/index.md)
+ [链接非EVM兼容链](/docs/mapo-stack/chains-connect/non-evm-chain/index.md)
+ [全链dapp开发](/docs/mapo-stack/omni-dapp/index.md)











