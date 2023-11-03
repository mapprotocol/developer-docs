---
title: EVM
description: An introduction to the EVM and how it relates to state, transactions, and smart contracts。
lang: en
---

`MAPO-Relay-Chain`使用以太坊EVM作为链的虚拟机，该虚拟机是所有`MAPO-Relay-Chain`帐户和智能合约依存的环境。 在链上任何给定的区块处，`MAPO-Relay-Chain`有且只有一个“规范”状态，而EVM虚拟机定义从一个区块到另一个区块计算新的有效状态的规则。以下`MAPO-Relay-Chain`统称为MAPO.

## prerequisites

Some basic familiarity with common terminology in computer science such as [bytes](https://wikipedia.org/wiki/Byte), [memory](https://wikipedia.org/wiki/Computer_memory), and a [stack](<https://wikipedia.org/wiki/Stack_(abstract_data_type)>) are necessary to understand the EVM. It would also be helpful to be comfortable with cryptography/blockchain concepts like [hash functions](https://wikipedia.org/wiki/Cryptographic_hash_function) and the [Merkle tree](https://wikipedia.org/wiki/Merkle_tree).



## the-state-transition-function

The EVM behaves as a mathematical function would: Given an input, it produces a deterministic output. It therefore is quite helpful to more formally describe Ethereum as having a **state transition function**:

```
Y(S, T)= S'
```

Given an old valid state `(S)` and a new set of valid transactions `(T)`, the Ethereum state transition function `Y(S, T)` produces a new valid output state `S'`

### state

In the context of MAPO, the state is an enormous data structure called a [modified Merkle Patricia Trie](/docs/base/mpt/index_en.md), which keeps all [accounts](/docs/base/accounts/index_en.md) linked by hashes and reducible to a single root hash stored on the blockchain.

### transactions

Transactions are cryptographically signed instructions from accounts. There are two types of transactions: those which result in message calls and those which result in contract creation.

Contract creation results in the creation of a new contract account containing compiled [smart contract](/docs/mapo-stack/compatible-evm/index_en.md#smart-contracts) bytecode. Whenever another account makes a message call to that contract, it executes its bytecode.

## evm-instructions

The EVM executes as a [stack machine](https://wikipedia.org/wiki/Stack_machine) with a depth of 1024 items. Each item is a 256-bit word, which was chosen for the ease of use with 256-bit cryptography (such as Keccak-256 hashes or secp256k1 signatures).

During execution, the EVM maintains a transient _memory_ (as a word-addressed byte array), which does not persist between transactions.

Contracts, however, do contain a Merkle Patricia _storage_ trie (as a word-addressable word array), associated with the account in question and part of the global state.

Compiled smart contract bytecode executes as a number of EVM opcodes, which perform standard stack operations like `XOR`, `AND`, `ADD`, `SUB`, etc. The EVM also implements a number of blockchain-specific stack operations, such as `ADDRESS`, `BALANCE`, `BLOCKHASH`, etc.

![A diagram showing the make up of the EVM](./evm-gas.jpg)


## smart-contracts

Smart contracts are the foundation of MAPO applications. They are computer programs stored on the blockchain that enable us to transform traditional contracts into digital contracts. Smart contracts adhere to logical rules, following the "if this then that" (IFTTT) structure. This means they execute exactly as programmed and are immutable.

Make sure you have a solid understanding of [accounts](/docs/base/accounts/index_en.md), [transactions](/docs/base/transactions/index_en.md), and the `EVM` discussed above before diving into smart contract learning.

### what-is-a-smart-contract

A smart contract is essentially a program that runs on the MAPO chain. It consists of a series of code (functions) and data (state) located at a specific address on the MAPO blockchain.

Smart contracts also function as [MAPO accounts](/docs/base/accounts/index_en.md), referred to as contract accounts. This means they have a balance and can be the target of transactions. However, they are not controlled by individuals; they are deployed on the network and run as autonomous programs. Individual users can interact with smart contracts by submitting transactions to execute specific functions within the contract. Smart contracts can define rules like traditional contracts and automatically enforce them through code. By default, smart contracts cannot be deleted, and interactions with them are irreversible.

### automation

One of the most significant advantages of smart contracts compared to traditional contracts is that the results are automatically executed when the contract conditions are met. There's no need to rely on a person to execute the results. In other words, smart contracts are trustless.

For example, you can write a smart contract to hold funds in trust for your children and allow them to withdraw the funds after a specific date. If the children attempt to withdraw the funds before the specified date, the smart contract will not execute. Alternatively, you can create a contract that automatically grants you digital ownership of a car after you've made a payment to the dealer.

### predictability

One of the biggest drawbacks of traditional contracts is the potential for human interpretation. For instance, two different judges may interpret a traditional contract differently. Their interpretations could lead to different judgments and different outcomes. Smart contracts eliminate the possibility of different interpretations. Instead, smart contracts execute precisely based on the conditions written in the contract code. This precision means that, in identical situations, smart contracts will produce the same results.

### public-record

Smart contracts can also be used for auditing and tracking. Because MAPO smart contracts are on a public blockchain, anyone can immediately track asset transfers and other relevant information. For example, you can check if someone has sent funds to your address. This transparency is one of the key features of blockchain technology.

### privacy-protection

Smart contracts also have the potential to enhance privacy. Since MAPO operates on an anonymous network (your transactions are publicly linked to unique cryptographic addresses, not your identity), you can protect your privacy from prying eyes. This anonymity is a significant feature of the MAPO network.

### visible-terms

The final point is that, just like contracts, you can review the contents of a smart contract before signing it or interacting with it in any way. What's even more remarkable is that the public transparency of contract terms means that anyone can review them. This transparency and open auditability are key features of smart contracts on the MAPO network.


### permissionless

Anyone can write a smart contract and deploy it to the blockchain network. All you need to do is learn how to code in a [smart contract language](/docs/mapo-stack/compatible-evm/solidity_en.md) and have enough MAPO coins to deploy your contract. Deploying a smart contract is technically a transaction, so just like you need to pay a gas fee for a simple MAPO coin transfer, you also need to pay a [gas fee](/docs/base/gas/index_en.md) for deploying a smart contract. However, the gas cost for contract deployment is much higher.

MAPO provides a developer-friendly smart contract programming language:

- Solidity

However, smart contracts must be compiled before they can be deployed so that the EVM virtual machine can interpret and store them. [more about compile](/docs/mapo-stack/compatible-evm/compile_en.md)

### composability

Smart contracts on MAPO are public and can be thought of as open APIs. This means you can call other smart contracts within your own, greatly extending the range of possible functionality. Contracts can even deploy other contracts.

Learn more about the [composability of smart contracts](/docs/mapo-stack/compatible-evm/composability_en.md).

### limitations

Smart contracts themselves cannot fetch information about "real-world" events because they cannot send HTTP requests. This is by design because relying on external information could impact consensus, which is crucial for security and decentralization.

This can be mitigated by using an `oracle`.


### smart-contract-resources

**OpenZeppelin 合约\*\*** - _安全智能合约开发库。_\*\*

- [openzeppelin.com/contracts/](https://openzeppelin.com/contracts/)
- [GitHub](https://github.com/OpenZeppelin/openzeppelin-contracts)
- [社区论坛](https://forum.openzeppelin.com/c/general/16)


## related-topics

- [Gas](/docs/base/gas/index.md)
