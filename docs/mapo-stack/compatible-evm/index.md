---
title: EVM虚拟机 (EVM)
description: 介绍EVM虚拟机及其与状态、交易和智能合约的关系。
lang: zh
---

`MAPO-Relay-Chain`使用以太坊EVM作为链的虚拟机，该虚拟机是所有`MAPO-Relay-Chain`帐户和智能合约依存的环境。 在链上任何给定的区块处，`MAPO-Relay-Chain`有且只有一个“规范”状态，而EVM虚拟机定义从一个区块到另一个区块计算新的有效状态的规则。以下`MAPO-Relay-Chain`统称为MAPO.

## 前提条件 {#prerequisites}

对计算机科学中常见术语的基本了解，如[字节](https://wikipedia.org/wiki/Byte)、[内存](https://wikipedia.org/wiki/Computer_memory)和[堆栈](<https://wikipedia.org/wiki/Stack_(abstract_data_type)>)是理解 EVM 的前提。 熟悉[哈希函数](https://wikipedia.org/wiki/Cryptographic_hash_function)和[默克尔树](https://wikipedia.org/wiki/Merkle_tree)等密码学/区块链概念也会很有帮助。


## 状态转换函数 {#the-state-transition-function}

EVM 的行为就像一个数学函数：在给定输入的情况下，它会产生确定性的输出。 因此，MAPO网络可以描述为具有**状态转换函数**非常有帮助：

```
Y(S, T)= S'
```

给定一个旧的有效状态 `（S）`> 和一组新的有效交易 `（T）`，状态转换函数 ` Y（S，T）` 产生新的有效输出状态` S'`

### 状态 {#state}

在MAPO环境中，状态是一种称为[改进版默克尔帕特里夏树](/docs/base/mpt/index.md)的巨大数据结构，它保存所有通过哈希关联在一起的[帐户](/docs/base/accounts/index.md)并可回溯到存储在区块链上的单个根哈希。

### 交易 {#transactions}

交易是来自帐户的密码学签名指令。 交易分为两种：一种是消息调用交易，另一种是合约创建交易。

合约创建交易会创建一个新的合约帐户，其中包含已编译的 [智能合约](/docs/mapo-stack/compatible-evm/index.md) 字节码。 每当另一个帐户对该合约进行消息调用时，它都会执行其字节码。

## EVM 说明 {#evm-instructions}

EVM 作为一个[堆栈机](https://wikipedia.org/wiki/Stack_machine)运行，其栈的深度为 1024 个项。 每个项目都是 256 位字，为了便于使用，选择了 256 位加密技术（如 Keccak-256 哈希或 secp256k1 签名）。

在执行期间，EVM 会维护一个瞬态*内存*（作为字可寻址的字节数组），该内存不会在交易之间持久存在。

然而，合约确实包含一个 Merkle Patricia _存储_ trie（作为可字寻址的字数组），该 trie 与帐户和部分全局状态关联。

已编译的智能合约字节码作为许多 EVM [opcodes](docs/mapo-stack/compatible-evm/index.md)执行，它们执行标准的堆栈操作，例如 `XOR`、`AND`、`ADD`、`SUB`等。 EVM 还实现了一些区块链特定的堆栈操作，如 `ADDRESS`、`BALANCE`、`BLOCKHASH` 等。

![表明 EVM 操作需要 Gas 的图表](./evm-gas.jpg)


## 相关主题 {#related-topics}

- [Gas](/docs/base/gas/index.md)
