---
title: Intro to MAP Protocol
description: MAP Protocol is a Bitcoin layer-2 as well as a peer-to-peer omnichain network focused on cross-chain interoperability.
lang: en
---

# Intro to MAP Protocol 
## What is a blockchain?
First conceptualized by an individual (or group) known as [Satoshi Nakamoto](https://en.wikipedia.org/wiki/Satoshi_Nakamoto) in 2008, blockchain technology is the backbone behind the revolutionary digital currency, Bitcoin. It's a decentralized ledger that records transactions across a network of computers. The defining feature of blockchain is that once a transaction is recorded, it becomes nearly impossible to alter, creating an immutable record of transactions.

The structure of a blockchain is a series of blocks, each containing a list of transactions. The data and state being stored in consecutive groups are referred to as “blocks”. To successfully send MAPO to someone on MAP Relay Chain, the data and state of this transaction need to be added to a block, or the transaction will fail.

Each block is then chained together with the previous one through a cryptographic hash, a unique digital fingerprint, ensuring the chain's integrity. The data in a block, i.e., the transaction information, cannot be changed without altering all the subsequent blocks, which would require changing the consensus of the entire network.

Blockchains can be public, like Bitcoin, where anyone can join and participate in the network, or private, where participation is restricted. Public blockchains are generally considered more secure due to their larger network of participants, which makes tampering more difficult.

Beyond cryptocurrency, blockchain has numerous potential applications. It can be used to securely record and verify a wide range of data, from financial transactions to supply chain management, voting systems, and beyond. The transparency, security, and decentralization offered by blockchain technology present exciting opportunities for innovation across industries.

A blockchain is run by a network of computers, often referred to as nodes. Each node in the network must agree upon each new block and the chain as a whole.  Nodes ensure that everyone interacting with the blockchain has the same data. To add a new block to the blockchain, nodes must reach a consensus. This is achieved through a consensus mechanism. The two most common mechanisms are Proof of Work (PoW) and Proof of Stake (Pos).

## What is MAP Protocol?
MAP Protocol is a Bitcoin layer-2 as well as a peer-to-peer omnichain network focused on cross-chain interoperability. It provides the essential omnichain infrastructure for achieving interoperability among blockchain-based assets, storage, and computing across EVM and non-EVM chains.

As an omnichain infrastructure, there are no single entities trusted for cross-chain communication on MAP Protocol. The only trust is put in code that leverages light clients’ self-verifying nature, thus ensuring cross-chain is done in a purely peer-to-peer way. When a cross-chain request occurs on the source chain, it will be transmitted by off-chain roles to the target chain. Light clients deployed on the source chain will then verify the validity of the cross-chain requests sent by the off-chain role in a peer-to-peer way.

Moreover, the security of MAP Protocol is further strengthened by the Bitcoin network. All related information such as epochs and MAP Relay Chain’s PoS consensus is written into the Bitcoin block as transactions. This will prevent [long range attacks](https://messari.io/report/long-range-attack) on the relay chain, thus using the Bitcoin network security mechanism to further ensure the security of the relay chain.

There are three layers structured in MAP Protocol — the Protocol Layer, MAP Omnichain Service Layer (MOS), and the Application Layer. 
### Protocol Layer
The Protocol layer consists of **MAP Relay Chain**, **light clients** deployed on each chain, and inter-chain **Maintainer** Program to update and maintain Light-Client status.

Similar to Ethereum, MAP Protocol also has a canonical computer called Virtual Machine (VM), which also makes MAP Protocol EVM-compatible. MAP Relay Chain at the Protocol layer proactively extends and supports heterogeneous blockchains’ features by incorporating different signatures, hash algorithms, and Merkle proofs of target chains as pre-compiled contracts. This enables the relay chain to be isomorphic with various types of blockchains and allows light clients to be deployed across different chains while functioning effectively.

Light clients deployed on each chain are independent, self-verifying in nature, and have verification finality. They are the verification network for cross-chain assets and data.

Maintainer is an independent inter-chain program responsible for updating light clients’ status. Since submitting transactions to blockchains would consume gas, Maintainers are rewarded with MAP tokens according to the useful work they have accomplished, e.g., the number of effective block headers submitted.
### MAP Omnichain Service Layer (MOS)
Similar to Google Mobile Services to Android developers, MOS empowers dApp developers to build applications with ease. It is the execution layer for omnichain assets and data.DApp developers can independently run MOS or use services provided by MOS. MOS consists of Vaults & Data deployed on each chain, and the Messenger program to transmit messages between chains.

On the source chain, **Vaults & Data** are responsible for receiving assets or data and triggering an event for Messengers to listen to. On the relay chain or target chain, Vault & Data are responsible for receiving cross-chain messages transmitted by Messengers and then through an internal component conducting cross-chain transaction verification. DApp developers can utilize Vaults & Data in MOS and share Vaults & Data liquidity with other applications.

**Messenger** is an independent inter-chain program. It is an SDK, deployed, operated, and maintained by dApp developers. DApp developers can also independently and flexibly incentivize messenger contributors to transmit omnichain messages for the dApp.
### Application Layer
The Application layer consists of specific use cases of MAP Protocol’s omnichain infrastructure. This can be omnichain and fully on-chain GameFi, omnichain lending, omnichain DAO, etc. 

DApp developers only need to deploy their dApps on MAP Relay Chain with the completed MOS  module to enjoy the privilege of connecting the entire blockchain world’s liquidity. 
## Characteristics of MAP Protocol
MAP Protocol has many unique characteristics that set it apart in the blockchain ecosystem. These features include:
- **Omnichain Interoperability**: MAP Protocol allows point-to-point cross-chain interoperability between EVM and non-EVM chains.
- **Security Further Enhanced by the Bitcoin Network**: The MAP Protocol leverages the security mechanisms of the Bitcoin network to protect the relay chain, using light client self-verification features to secure cross-chain transactions.
- **Distributed Trust**: MAP Protocol is decentralized, with no single entity in control, and all participants rely on the code for operations.
- **Flexibility**: The protocol allows the integration of different types of blockchains, including those with different signature schemes, hash algorithms, and Merkle proofs.

In summary, MAP Protocol is an innovative omnichain interoperability network designed to facilitate seamless communication and interaction between different blockchains. With its secure infrastructure, omnichain interoperability, and distributed trust, it provides developers and users with powerful tools for implementing complex blockchain interactions. As more dApps and services leverage MAP Protocol, we can expect a more interconnected and efficient blockchain ecosystem.
## Roadmap
### 2024 Q4
* Launch and open-source Omnichain Development SDK V2.
### 2024 Q3
* The release of the ‘Refactored Light Client Verification with ZK-Proof’ module as open source.
* Launch Omnichain Development SDK V1.
* Implement cross-chain interoperability support for Linea, Scroll, Solana, and Ton.
### 2024 Q2
* Test the “Refactored Light Client Verification with ZK-Proof” module.
* Officially release cross-chain interoperability with Tron, Optimism, Mantle, Arbitrum, and zkSync Era.
### 2024 Q1
* Officially release the extended cross-chain connections to Tron and Conflux.
* Test and open the MRC20 Omnichain issuance tool.
### 2023 Q4
* Extend cross-chain connections to Tron and Conflux and conduct testing.
* Officially upgrade to become a Bitcoin layer 2 for peer-to-peer cross-chain interoperability.
* Release the upgraded official website and technical documentation.
* Launch a support plan for the BRC20 ecosystem.
> [View MAP Protocol history](https://docs.mapprotocol.io/learn/roadmap) to learn about the roadmap from 2019 Q3 to 2023 Q3.
## Essential concepts
### Blockchain
A blockchain is a distributed, decentralized ledger that records transactions across multiple computers in a way that ensures each transaction can only be added once and is irreversible, enhancing the security and integrity of the data.
### MAPO
MAPO is the native cryptocurrency of MAP Protocol. When you send MAPO or use an app on MAP Protocol, you'll pay a fee in MAPO to use the MAP Protocol network. This fee serves as an incentive for a block producer to process and verify your activity on the network.
### Nodes
Nodes are individual computers that connect to a blockchain network and have a copy of the entire ledger. They participate in the network's consensus mechanism, validate transactions, and maintain the integrity and security of the blockchain.
### Smart contracts
Smart contracts are self-executing contracts with the terms of the agreement directly written into lines of code. They automatically enforce and execute the contract terms when predefined conditions are met, without the need for intermediaries.
### Turing-complete
A Turing-complete system, in the context of blockchain, refers to a smart contract platform that can perform any computable function, given enough resources. This enables the creation of complex decentralized applications on the blockchain.
### Long range attack
A long range attack in blockchain is a form of attack where an adversary attempts to rewrite a blockchain's history starting from a point far back in the chain. They would secretly build an alternative chain, which, if longer than the current chain, could potentially replace the existing blockchain, compromising its integrity.


