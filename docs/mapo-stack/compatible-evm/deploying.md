---
title: 部署智能合约
description:
lang: zh
---

需要部署智能合约才能提供给`MAPO-Relay-Chain`网络的用户使用。以下`MAPO-Relay-Chain`统称为MAPO.

要部署一个智能合约，只需发送一个包含编译后的智能合约代码的MAPO交易，而不需要指定任何收件人。

## 前置要求 {#prerequisites}

在部署智能合约之前，您需要理解[MAPO网络](docs/base/mapo-relay-chain/index.md), [交易](/docs/base/transactions/index.md)和[详解智能合约](/docs/mapo-stack/compatible-evm/anatomy.md)。

部署一个合约也需要耗费MAPO币 (MAPO)，因为他们被存储在区块链上，所以您应该熟悉MAPO的[燃料和费用](/docs/base/gas/index.md)。

最后，您需要在部署之前编译您的合约，所以请确保您已经阅读了[编译智能合约](/docs/mapo-stack/compatible-evm/compile.md)。

## 如何部署智能合约 {#how-to-deploy-a-smart-contract}

### 您所需要的 {#what-youll-need}

- 您的合约字节码 – 这是通过[编译](/docs/mapo-stack/compatible-evm/compile.md)获得的。
- 用作燃料的MAPO币 – 像其他交易一样，您需要设定燃料限制，这样就知道部署合约比简单的MAPO币交易需要更多的燃料。
- 一个部署脚本或插件。
- 访问[MAPO节点](/docs/base/mapo-relay-chain/public-service.md)，连接到公共节点来访问。

### 部署智能合约的步骤 {#steps-to-deploy}

所涉及的具体步骤将取决于您使用的工具。 例如，查看[关于部署合约的安全帽文档](https://hardhat.org/guides/deploying.html)或[关于网络和应用程序部署的 Truffle 文档](https://www.trufflesuite.com/docs/truffle/advanced/networks-and-app-deployment)。 这是两个最受欢迎的智能合约部署工具，它们涉及到编写脚本来处理部署步骤。

一旦部署，您的合约将有一个MAPO地址。

## 相关工具 {#related-tools}

**Remix - _Remix 集成开发环境可以开发、部署和管理类似区块链的智能合约。_**

- [Remix](https://remix.ethereum.org)

**Tenderly - _Web3 开发平台，提供调试、可观测性和基础设施构建基块，用于开发、测试、监测和操作智能合约_**

- [tenderly.co](https://tenderly.co/)
- [相关文档](https://docs.tenderly.co/)
- [GitHub](https://github.com/Tenderly)
- [Discord](https://discord.gg/eCWjuvt)

**安全帽 - _用于编译、部署、测试和调试的开发环境_**

- [hardhat.org](https://hardhat.org/getting-started/)
- [关于部署合约的文档](https://hardhat.org/guides/deploying.html)
- [GitHub](https://github.com/nomiclabs/hardhat)
- [Discord](https://discord.com/invite/TETZs2KK4k)

**Truffle -** **_开发环境、测试框架、部署通道及其他工具。_**

- [trufflesuite.com](https://www.trufflesuite.com/)
- [关于网络和应用部署的文档](https://www.trufflesuite.com/docs/truffle/advanced/networks-and-app-deployment)
- [GitHub](https://github.com/trufflesuite/truffle)
