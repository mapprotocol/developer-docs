---
title: 智能合约语言
description: Solidity智能合约语言的概述。
lang: zh
---

`MAPO-Relay-Chain`的智能合约主要使用`Solidity`语言作为编程语言，以下`MAPO-Relay-Chain`统称为MAPO.

## 前提条件 {#prerequisites}

如果已经有编程语言（特别是关于 JavaScript 或 Python）知识，可以帮助您体验到智能合约语言的差异。 同时，我们建议您在深入理解语言差异之前，先理解作为概念的智能合约。 [智能合约简介](/docs/mapo-stack/compatible-evm/index.md#智能合约smart-contracts)。

## Solidity {#solidity}

- 执行智能合约的目标导向高级语言。
- 受 C++ 影响最深的大括号编程语言。
- 静态类型（编译时已知变量类型）。
- 支持：
  - 继承（您可以拓展其它合约）。
  - 库（您可以创建从不同的合约调用的可重用代码 - 就像静态函数在其它面向对象编程语言的静态类中一样）。
  - 复杂的用户自定义类型。

### 重要链接 {#important-links}

- [相关文档](https://docs.soliditylang.org/en/latest/)
- [Solidity 语言网站](https://soliditylang.org/)
- [Solidity 示例](https://docs.soliditylang.org/en/latest/solidity-by-example.html)
- [GitHub](https://github.com/ethereum/solidity/)
- [Solidity Gitter Chatroom](https://gitter.im/ethereum/solidity/) 桥接到 [Solidity Matrix Chatroom](https://matrix.to/#/#ethereum_solidity:gitter.im)
- [备忘单](https://reference.auditless.com/cheatsheet)
- [Solidity 博客](https://blog.soliditylang.org/)
- [Solidity Twitter](https://twitter.com/solidity_lang)

### 合约示例 {#example-contract}

```solidity
/ SPDX-License-Identifier: GPL-3.0
pragma solidity >= 0.7.0;

contract Coin {
    // The keyword "public" makes variables
    // accessible from other contracts
    address public minter;
    mapping (address => uint) public balances;

    // Events allow clients to react to specific
    // contract changes you declare
    event Sent(address from, address to, uint amount);

    // Constructor code is only run when the contract
    // is created
    constructor() {
        minter = msg.sender;
    }

    // Sends an amount of newly created coins to an address
    // Can only be called by the contract creator
    function mint(address receiver, uint amount) public {
        require(msg.sender == minter);
        require(amount < 1e60);
        balances[receiver] += amount;
    }

    // Sends an amount of existing coins
    // from any caller to an address
    function send(address receiver, uint amount) public {
        require(amount <= balances[msg.sender], "Insufficient balance.");
        balances[msg.sender] -= amount;
        balances[receiver] += amount;
        emit Sent(msg.sender, receiver, amount);
    }
}
```

这个示例应该能让您感觉到 Solidity 合约语法是什么样子的。 关于函数和变量的详细描述，[请查看文档](https://docs.soliditylang.org/en/latest/contracts.html)。


### Solidity 的优点是什么？ {#solidity-advantages}

- 对于初学者有很多教程和学习工具。
- 提供出色的开发者工具。
- Solidity 拥有庞大的开发人员社区，这意味着您很可能会很快找到问题的答案。

## 延伸阅读 {#further-reading}

- [OpenZeppelin 的 Solidity 合约库](https://docs.openzeppelin.com/contracts)
- [Solidity 示例](https://solidity-by-example.org)
