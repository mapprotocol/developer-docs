---
title: 开发网络
description: 对MAPO-Relay-Chain应用的开发网络环境与开发工具的概览。
lang: zh
---

以下`MAPO-Relay-Chain`统称为MAPO。

当使用智能合约来开发一个MAPO应用时，您可能想要在部署之前在本地查看它是如何工作的。

这和在本地运行一个本地网页服务器相似。为了测试您的去中心化应用程序，您可以使用开发网络创建一个本地的区块链。 这些MAPO开发网络提供了能够比公共测试网更快的迭代功能。


## 什么是开发网络？ {#what-is-a-development-network}

实质上开发网络是指哪些对本地开发特殊设计的MAPO客户端。

### 公共MAPO测试链 {#public-testchains}

MAPO维护了两个公共测试网：MAPO测试网 和 MAPO开发网。[更多信息](/docs/base/mapo-relay-chain/public-service.md)。

### Ganache {#ganache}

MAPO虚拟机兼容以太坊EVM虚拟机，所以也支持Ganache，您可以用它来运行测试，执行命令，并在控制链的运行方式时检查状态。

Ganache 提供了一个桌面应用程序 (Ganache UI) 以及一个命令行工具 (`ganache-cli`)。 它是 Truffle 工具套装的一部分。

- [网站](https://www.trufflesuite.com/ganache)
- [GitHub](https://github.com/trufflesuite/ganache)
- [相关文档](https://www.trufflesuite.com/docs/ganache/overview)

### 安全帽网络 {#hardhat-network}

MAPO也支持hardhat。 该网络允许您部署合约，运行测试并调试代码。

- [网站](https://hardhat.org/)
- [GitHub](https://github.com/nomiclabs/hardhat)




