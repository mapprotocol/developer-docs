---
title: 交易
description: MAPO-Relay-Chain交易 – 工作原理、数据结构以及如何通过应用发送。
lang: zh
---

交易是由帐户发出，带密码学签名的指令。 帐户将发起交易以更新`MAPO-Relay-Chain`网络的状态。 最简单的交易是将 MAPO币 从一个账户转到另一个帐户。以下`MAPO-Relay-Chain`统称为MAPO.

## 前提条件 {#prerequisites}

为了帮助您更好地理解这个页面，我们建议您先阅读[账户](docs/base/accounts/index.md)和我们的[MAPO简介](docs/base/intro-to-mapo/index.md)。

## 什么是交易？ {#whats-a-transaction}

MAPO交易是指由外部持有账户发起的行动，换句话说，是指由人管理而不是智能合约管理的账户。 例如，如果 Bob 向 Alice 发送 1 MAPO币，则 Bob 的帐户必须减少 1 MAPO币，而 Alice 的账户必须增加 1 MAPO币。 交易会造成状态的改变。

改变 EVM 状态的交易需要广播到整个网络。 任何节点都可以广播在MAPO虚拟机上执行交易的请求；此后，验证者将执行交易并将由此产生的状态变化传播到网络的其他部分。

交易需要付费并且必须包含在一个有效区块中。 为了使本概述更加简洁，我们将另行介绍燃料费和验证。

所提交的交易包括下列信息：

- `from` - 发送者的地址，该地址将签署交易。 这将是一个外部帐户，因为合约帐户不能发送交易。
- `recipient` – 接收地址（如果是外部帐户，交易将传输值。 如果是合约帐户，交易将执行合约代码）
- `signature` – 发送者的标识符。 当发送者的私钥签署交易并确保发送者已授权此交易时，生成此签名。
- `nonce` - 一个有序递增的计数器，表示来自帐户的交易数量
- `value` – 发送者向接收者转移的MAPO币数量（面值为 WEI，1 个MAPO币 = 1e+18wei）
- `data` – 可包括任意数据的可选字段
- `gasLimit` – 交易可以消耗的最大数量的燃料单位。 
- `maxPriorityFeePerGas` - 作为小费提供给验证者的已消耗燃料的最高价格
- `maxFeePerGas` - 愿意为交易支付的每单位燃料的最高费用（包括 `baseFeePerGas` 和 `maxPriorityFeePerGas`）

燃料是指验证者处理交易所需的计算。 用户必须为此计算支付费用。 `gasLimit` 和 `maxPriorityFeePerGas` 决定支付给验证者的最高交易费。 [关于燃料的更多信息](docs/base/gas/index.md)。


### `data`字段 {#the-data-field}

绝大多数交易都是从外部所有的帐户访问合约。 合约用 Solidity 语言编写，并根据应用程序二进制接口 (ABI)解释其`data`字段。

前四个字节使用函数名称和参数的哈希指定要调用的函数。调用数据的其余部分是参数。


## 交易类型 {#types-of-transactions}

MAPO有几种不同类型的交易：

- 常规交易：从一个帐户到另一个帐户的交易。
- 合约部署交易：没有“to”地址的交易，数据字段用于合约代码。
- 执行合约：与已部署的智能合约进行交互的交易。 在这种情况下，“to”地址是智能合约地址。

### 关于燃料 {#on-gas}

如上所述，执行交易需要耗费[燃料](docs/base/gas/index.md)。 简单的转账交易需要 21000 单位燃料。

因此，如果 Bob 要在 `baseFeePerGas` 为 100 Gwei 且 `maxPriorityFeePerGas` 为 10 Gwei 时给 Alice 发送一个MAPO币，Bob 需要支付以下费用：

```
(100 + 10) * 21000 = 2,310,000 gwei
--或--
0.00231 MAPO
```

Bob 的帐户将会扣除 **1.00231 个MAPO币**（1 个MAPO币给 Alice，0.00231 个MAPO币作为燃料费用）

Alice 的帐户将会增加 **+1.0 MAPO币**

基础费将会燃烧 **-0.0021 MAPO币**

验证者获得 **0.000210 个MAPO币**的小费

任何智能合约交互也需要燃料。任何未用于交易的燃料都会退还给用户帐户。

## 交易生命周期 {#transaction-lifecycle}

交易提交后，就会发生以下情况：

1. 以加密方式生成的交易哈希（txid）
2. 然后，该交易被广播到网络，并添加到由所有其他待处理的网络交易组成的交易池中。
3. 验证者必须选择你的交易并将它包含在一个区块中，以便验证交易并认为它“成功”。
4. 随着时间的流逝，包含你的交易的区块将升级成“合理”状态，然后变成“最后确定”状态。 通过这些升级，可以进一步确定 你的交易已经成功并将无法更改。 

## 相关主题 {#related-topics}

- [帐户](docs/base/accounts/index.md)
- [虚拟机 (EVM)](docs/mapo-stack/compatible-evm/index.md)
- [燃料](docs/base/gas/index.md)
