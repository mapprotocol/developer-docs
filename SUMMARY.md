# Summary

本文档旨在帮助你认识MAPO，或使用它构建你想构建的去中心化应用，或将一条区块链接入MAPO实现与其他区块链的互通。该文档介绍了MAPO的概念，解释了MAPO技术堆栈，以及MAPO应用的使用案例。

基于开源的社区准则，你可以随时提出新的主题，添加新的内容，并在认为可能有用的地方提供示例。所有文档都可以通过github编辑，并会存储到去中心化存储设施`Arweave`。如果不确定如何操作，请遵循[说明进行](docs/editing-markdown.md)。

如果这是你第一次尝试MAPO开发，建议你从头开始阅读，这不仅可以让你更好的熟悉MAPO，其中涉及区块链底层的技术以及ZK等内容，也会让你对点对点的代码信任有一个全新的认识。[english](https://mapo.gitbook.io/docs-en/)

## 基础主題

* [脉波简介](docs/base/intro-to-mapo/index.md)
* [MAPO币](docs/base/intro-to-mapo/mapo-coin.md)
* [全链去中心化应用](docs/base/omnichain-dapp/index.md)
* [全链应用与单链或多链应用的区别](docs/base/omnichain-dapp/different.md)
* [第三方信任跨链与点对点跨链方案区别](docs/base/omnichain-dapp/the-other.md)
* [帐户](docs/base/accounts/index.md)
* [交易](docs/base/transactions/index.md)
* [区块](docs/base/block/index.md)
* [MPT树](docs/base/mpt/index.md)
* [RLP编码](docs/base/rlp/index.md)
* [Gas费用](docs/base/gas/index.md)
* [消息跨链](docs/base/cross-chain-message/index.md)
* [轻客户端](docs/base/light-client/index.md)
  * [MAPO轻客户端](docs/base/light-client/MapoLightClient.md)
* [全链开发组件层MOS](docs/base/mos/index.md)
  * [MOS接口和功能](docs/base/mos/mos\_interface.md)
  * [MOS的部署](docs/base/mos/mos\_deploy.md)
  * [Messenger](docs/base/mos/Messenger.md)
* [中继链(atlas)](docs/base/mapo-relay-chain/nodes/architecture.md)
  * 节点架构
    * [中继链（atlas）架构 - 区块和交易结构](docs/base/mapo-relay-chain/nodes/architecture.md)
    * 创世
      * [创世配置 - 介绍创世配置文件](docs/base/mapo-relay-chain/nodes/genesis-config.md)
      * [创世合约](docs/base/mapo-relay-chain/genesis-contract/index.md)
        * ABI
          * [Accounts](docs/base/mapo-relay-chain/genesis-contract/accounts.md)
          * [Election](docs/base/mapo-relay-chain/genesis-contract/election.md)
          * [EpochRewards](docs/base/mapo-relay-chain/genesis-contract/epoch-rewards.md)
          * [LockedGold](docs/base/mapo-relay-chain/genesis-contract/locked-gold.md)
          * [Validators](docs/base/mapo-relay-chain/genesis-contract/validators.md)
        * [地址](docs/base/mapo-relay-chain/genesis-contract/address.md)
        * [部署](docs/base/mapo-relay-chain/genesis-contract/deploy.md)
    * [预编译合约 - 支持的预编译合约](docs/base/mapo-relay-chain/precompile-contract.md)
    * 协议
      * [Proof of Stake](docs/base/mapo-relay-chain/protocol/pos.md)
      * [共识](docs/base/mapo-relay-chain/protocol/consensus.md)
      * [选举](docs/base/mapo-relay-chain/protocol/election.md)
      * [奖励](docs/base/mapo-relay-chain/protocol/rewards.md)
      * [治理](docs/base/mapo-relay-chain/protocol/governance.md)
  * 部署节点 - 包括公共RPC节点
    * [运行节点（中继链）](docs/base/mapo-relay-chain/nodes/run-a-node.md)
    * [归档节点（中继链）](docs/base/mapo-relay-chain/nodes/archive-nodes.md)
    * [引导节点（中继链）](docs/base/mapo-relay-chain/nodes/bootnodes.md)
    * [验证节点 （中继链）](docs/base/mapo-relay-chain/nodes/validator-nodes.md)
    * [RPC节点（中继链）](docs/base/mapo-relay-chain/nodes/rpc-nodes.md)
  * [Marker工具 - atlas的简易客户端工具](docs/base/mapo-relay-chain/marker/overview.md)
    * [Genesis](docs/base/mapo-relay-chain/marker/genesis.md)
    * [Validator](docs/base/mapo-relay-chain/marker/validator.md)
    * [Vote](docs/base/mapo-relay-chain/marker/vote.md)
    * [Common](docs/base/mapo-relay-chain/marker/common.md)
  * [搭建私有网络](docs/base/mapo-relay-chain/make-private-network.md)
  * 公共网络服务信息
    * [公共网络](docs/base/mapo-relay-chain/public-service.md)
  * 示例
    * [如何成为一个 Validator 并加入到 Atlas 网络中](docs/base/mapo-relay-chain/example/how-to-become-a-new-validator.md)
    * [如何成为一个 Validator 并加入到 Atlas 网络中\[高级\]](docs/base/mapo-relay-chain/example/how-to-become-a-new-validator-advanced.md)
* [Compass(maintainer，messenger)](docs/base/Compass/index.md)
  * [Compass - 架构及模块说明](docs/base/Compass/index.md#compass---架构及模块说明)
  * [Compass配置参数](docs/base/Compass/index.md#compass环境与部署)
  * [Compass环境与部署](docs/base/Compass/index.md#compass环境与部署)
  * [Compass二次开发 - 基于compass定义自己的路由服务](docs/base/Compass/index.md#compass二次开发---基于compass定义自己的路由服务)

## MAPO技术堆栈

* [堆栈](docs/mapo-stack/stack/index.md)
* [EVM兼容](docs/mapo-stack/compatible-evm/index.md)
  * [智能合约语言](docs/mapo-stack/compatible-evm/solidity.md)
  * [智能合约结构](docs/mapo-stack/compatible-evm/anatomy.md)
  * [智能合约库](docs/mapo-stack/compatible-evm/libraries.md)
  * [编译智能合约](docs/mapo-stack/compatible-evm/compile.md)
  * [测试智能合约](docs/mapo-stack/compatible-evm/testing.md)
  * [部署智能合约](docs/mapo-stack/compatible-evm/deploying.md)
  * [可组合性](docs/mapo-stack/compatible-evm/composability.md)
  * [智能合约安全性](docs/mapo-stack/compatible-evm/security.md)
  * [智能合约形式化验证](docs/mapo-stack/compatible-evm/formal-verification.md)
  * [开发框架](docs/mapo-stack/compatible-evm/frameworks.md)
  * [开发网络](docs/mapo-stack/compatible-evm/dev-network.md)
* [实现跨链互通](docs/mapo-stack/chains-connect/index.md)
  * [EVM兼容链的跨链互通](docs/mapo-stack/chains-connect/evm-chain/index.md)
    * [轻客户端验证](docs/mapo-stack/chains-connect/evm-chain/index.md#light-client层)
    * [轻客户端状态更新](docs/mapo-stack/chains-connect/evm-chain/index.md#maintainer开发)
    * [MOS层](docs/mapo-stack/chains-connect/evm-chain/index.md#mos层)
  * [非EVM兼容链的跨链互通](docs/mapo-stack/chains-connect/non-evm-chain/index.md)
    * [轻客户端验证](docs/mapo-stack/chains-connect/non-evm-chain/index.md#light-client层)
    * [轻客户端状态更新](docs/mapo-stack/chains-connect/non-evm-chain/index.md#maintainer开发)
    * [MOS层](docs/mapo-stack/chains-connect/non-evm-chain/index.md#mos层)
* [如何开发全链应用](docs/mapo-stack/omni-dapp/index.md)
* SDK/API - mapo支持的API
  * [MOS接口](docs/sdk/mos/index.md)
  * [轻客户端接口](docs/sdk/light-client/index.md)
  * 中继链RPC
    * [json-rpc](docs/sdk/mapo-relay-chain/json-rpc/index.md)
      * [atlas json rpc](docs/sdk/mapo-relay-chain/json-rpc/atlas-json-rpc.md)
      * [atlas consensus rpc](docs/sdk/mapo-relay-chain/json-rpc/atlas-consensus-rpc.md)
    * [javaScript sdk](docs/sdk/mapo-relay-chain/javaScript.md)
    * [go-sdk](mapo-ji-shu-dui-zhan/sdkapi-mapo-zhi-chi-de-api/zhong-ji-lian-rpc/go-sdk.md)
  * 后端API
    * [浏览器API](docs/sdk/backend/index.md)
    * 数据统计与分析API

## 零知识证明(zk)
