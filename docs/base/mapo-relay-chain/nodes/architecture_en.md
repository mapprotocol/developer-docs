---
title: Atlas架构
description:
lang: zh
---

Atlas is the name of the `Mapo-Relay-Chain` in the Mapo Protocol. It is a blockchain that is Ethereum Virtual Machine (
EVM) compatible and an open cryptographic protocol that allows applications to transact and execute smart contracts in a
secure and decentralized manner. The Atlas blockchain code shares a common ancestry with Ethereum and maintains full EVM
compatibility for smart contracts. However, it uses a Byzantine Fault Tolerant (BFT) consensus mechanism (Proof of
Stake) instead of Proof of Work, and has different block and transaction formats, as well as gas payment and pricing
mechanisms. Atlas is primarily implemented based on the [geth](https://github.com/ethereum/go-ethereum) node. Atlas is
compatible with EVM and supports application development tools such as `web3.js`, `remix`, and `hardhat`.

The main modules of Atlas are as follows:

## Atlas Blockchain

The blockchain of the `Atlas` node is the local database of the Atlas blockchain. It is a database that contains blocks,
transactions, account states, and configuration information. This database not only supports persistent storage of data
but also efficient querying and updating of data to ensure that the node can stay synchronized with the Atlas network,
process transactions, and execute smart contracts. It is the core data storage component of the Atlas network and stores
the entire blockchain data.

+ `Blockchain Database` is one of the core components of the Atlas node, responsible for storing the entire Atlas
  blockchain data. The database uses `LevelDB` as the backend storage engine for efficient management and retrieval of
  blockchain data.

+ `Blocks` are the basic units of data in the Atlas blockchain. Each block contains a certain number of transactions,
  timestamps, and the hash of the parent block, among other information. These blocks are linked together in
  chronological order to form the blockchain. For more information, please refer
  to [/docs/base/block/index.md](/docs/base/block/index.md).

+ `Transactions` are a series of transactions included in a block. These transactions include value transfers and smart
  contract invocations in the Atlas network. Each transaction has information such as the sender, receiver, amount, and
  smart contract invocation data. For more information, please refer
  to [/docs/base/transactions/index.md](/docs/base/transactions/index.md).

+ `State Storage` is a critical data structure that tracks the state of each account in the Atlas blockchain. This
  includes the balance of accounts, smart contract code, storage data, etc. State storage is implemented based on the
  Merkle Patricia Trie (MPT) tree data structure for efficient retrieval and updating of account states.

+ `Transaction Pool` is a buffer maintained by the Atlas node to store pending transactions. Transactions in the
  transaction pool are waiting to be included in new blocks.

+ `Accounts`: The blockchain database stores information about all Atlas accounts. This includes external accounts (
  controlled by private keys) and smart contract accounts (controlled by smart contract code). For more information,
  please refer to [/docs/base/accounts/index.md](/docs/base/accounts/index.md).

+ `Account State` is the current state of an account, including balance, smart contract code, storage data, and other
  relevant information. Account states are part of the state storage.

+ `Blockchain Indexing`: To improve query performance, Atlas nodes often create various indexes such as transaction
  index, log index, account balance index, etc. These indexes allow fast retrieval of specific data without scanning the
  entire blockchain.

## Consensus Mechanism

The consensus mechanism adopted by the `Atlas` blockchain is IBFT (Istanbul Byzantine Fault Tolerance), which is a
variant of the BFT (Byzantine Fault Tolerance) consensus algorithm. IBFT serves as the consensus layer of the `Atlas`
blockchain and is based on the principles of Proof of Stake (PoS) and Byzantine fault tolerance, aiming to ensure
decentralization and security of the network. Byzantine fault tolerance refers to the ability of a system to continue
functioning properly even if some of its nodes are faulty or exhibit malicious behavior. The `Atlas` consensus layer
primarily consists of the BFT protocol and a set of system contracts. The following is a detailed explanation of the
IBFT consensus mechanism in `Atlas` and the content and functionality of the system contracts.

### IBFT Consensus Mechanism:

The core idea of the IBFT consensus mechanism is that a group of validators is responsible for validating transactions
and generating new blocks. These validators are selected and have the authority to participate in the consensus process.
The core of the consensus is the voting mechanism, where each validator votes on the validity of a block. Only when a
sufficient number of validators agree, the new block is accepted. This approach ensures fast finality, where blocks and
transactions are confirmed once consensus is reached, without the need to wait for multiple confirmation blocks.

+ Validators: Validators in the Atlas blockchain are network participants who are responsible for generating blocks and
  validating transactions. They are selected and have the authority to produce new blocks. Atlas selects a group of
  validators through staking and the consensus algorithm in the system contracts, and these validators take turns to
  produce blocks.

+ Voting Mechanism: The IBFT consensus uses a voting mechanism to achieve consensus. Validators vote on the validity of
  blocks, including validating transactions and determining the proposer of the next block. This voting mechanism helps
  prevent Byzantine faults and ensures the reliability of consensus (one validator, one vote).

+ Fast Finality: The IBFT consensus allows for fast finality, meaning that once consensus is reached, transactions are
  immediately confirmed without the need to wait for multiple blocks for confirmation.

+ Decentralization: The IBFT consensus mechanism maintains decentralization by rotating the production of blocks among
  validators, without relying on a single central node to generate all the blocks.

### System Contracts:

The `Atlas` system contracts are a set of smart contracts used to manage and control various aspects of the `Atlas`
blockchain. Here are some important `Atlas` system contracts and their functionalities:

+ Election Contract: The Election contract defines the rules for the election of validators, including the eligibility
  criteria for validator candidates, election timetable, and related parameters. It is closely related to the IBFT
  consensus mechanism as it determines which validators are eligible to participate in the consensus.

+ LockedGold Contract: The LockedGold contract manages the collateral of users, which is used for participation in
  network governance, validation work, and other purposes. It is related to the IBFT consensus mechanism as it ensures
  that validators have sufficient collateral to fulfill their responsibilities.

+ Governance Contract: The Governance contract supports the governance of the `Atlas` community. Holders can participate
  in voting, propose and approve proposals through the governance contract to influence the network's rules and
  parameters. These proposals may be related to the IBFT consensus mechanism, such as changing the validator list or
  consensus rules.

In summary, the IBFT consensus mechanism of `Atlas` ensures the security and consensus of the network, while the `Atlas`
system contracts define the functionalities related to the network's rules, governance, collateral management, and more.
Together, they build the consensus mechanism of the `Atlas` blockchain, supporting the development of stablecoin systems
and decentralized financial applications.

## Network Layer

The network layer of `Atlas` is responsible for communication with other nodes in the `Atlas` network. It utilizes the
P2P module of Ethereum to transmit data and messages. The network layer allows nodes to perform handshakes, exchange
blocks, transactions, and state information. It enables nodes to broadcast, receive, and process messages within the
network to maintain synchronization across the entire network.