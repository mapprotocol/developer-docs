---
title: smart contract language
description: Solidity language。
lang: en
---

MAPO-Relay-Chain’s smart contracts mainly use Solidity language as the programming language,The following `MAPO-Relay-Chain` is collectively referred to as MAPO.

## prerequisites

Previous knowledge of programming languages, especially of JavaScript or Python, can help you make sense of differences in smart contract languages. We also recommend you understand smart contracts as a concept before digging too deep into the language comparisons. [Intro to smart contracts](/docs/mapo-stack/compatible-evm/index_en.md#smart-contracts).

## solidity

- Object-oriented, high-level language for implementing smart contracts.
- Curly-bracket language that has been most profoundly influenced by C++.
- Statically typed (the type of a variable is known at compile time).
- Supports:
  - Inheritance (you can extend other contracts).
  - Libraries (you can create reusable code that you can call from different contracts – like static functions in a static class in other object oriented programming languages).
  - Complex user-defined types.

### important-links

- [Documentation](https://docs.soliditylang.org/en/latest/)
- [Solidity Language Portal](https://soliditylang.org/)
- [Solidity by Example](https://docs.soliditylang.org/en/latest/solidity-by-example.html)
- [GitHub](https://github.com/ethereum/solidity/)
- [Solidity Gitter Chatroom](https://gitter.im/ethereum/solidity/) 桥接到 [Solidity Matrix Chatroom](https://matrix.to/#/#ethereum_solidity:gitter.im)
- [Cheat Sheet](https://reference.auditless.com/cheatsheet)
- [Solidity Blog](https://blog.soliditylang.org/)
- [Solidity Twitter](https://twitter.com/solidity_lang)

### example-contract

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

This example should give you a sense of what Solidity contract syntax is like. For a more detailed description of the functions and variables, [see the docs](https://docs.soliditylang.org/en/latest/contracts.html).


### solidity-advantages

- If you are a beginner, there are many tutorials and learning tools out there. 
- Good developer tooling available.
- Solidity has a big developer community, which means you'll most likely find answers to your questions quite quickly.

## further-reading

- [OpenZeppelin](https://docs.openzeppelin.com/contracts)
- [Solidity example](https://solidity-by-example.org)
