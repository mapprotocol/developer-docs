---
title: Block
description: Overview of Blocks in MAPO-Relay-Chain - Their Data Structure, Purpose, and How Blocks are Generated
lang: en
---


A block refers to a combination of transactions and contains the hash of the previous block in the chain. This links blocks together, forming a chain, because the hash is derived from the block's data. This prevents fraud because any change in any previous block will render all subsequent blocks invalid, and all hashes will change, which will be noticed by everyone running the blockchain. The term `MAPO-Relay-Chain` is collectively referred to as `MAPO`.

## prerequisites

The concept of blocks is very beginner-friendly. To better understand this page, we recommend reading about [accounts](/docs/base/accounts/index_en.md), [transactions](/docs/base/transactions/index_en.md), and our [MAPO introduction](/docs/base/intro-to-mapo/index_en.md) first.

## why-blocks

To ensure that all participants on the `MAPO` network stay in sync and reach consensus on the exact history of transactions, we divide transactions into multiple blocks. This means that dozens or even hundreds of transactions are submitted, agreed upon, and synchronized simultaneously.


## how-blocks-work

In order to maintain the transaction history, blocks are strictly ordered (each newly created block contains a reference to its parent block), and transactions within blocks are also strictly ordered. With very few exceptions, at any given time, all participants on the network agree on the exact number and history of blocks, and they are actively working to batch the current pending transaction requests into the next block. Once a block is constructed and confirmed, it is propagated throughout the network, and all nodes append this block to the end of their blockchain. Approximately every 5 seconds, a new block is generated.

## proof-of-work-protocol

Proof of Stake (PoS):

- PoS is a consensus mechanism in which validators must stake a certain amount of MAPO coins(1000000) as collateral to prevent malicious behavior. This helps secure the network because if dishonest activity occurs and can be proven, a portion or all of the staked amount may be destroyed.

- In each slot (a 5-second time interval), a validator is randomly selected as a block proposer. They package, execute, and sign transactions, and determine a new "state." They package this information into a block and broadcast it to other validators.

- Other validators who receive the new block then re-execute the transactions included in the block to ensure they agree on the proposed changes to the global state. Assuming the block is valid, validators sign it and broadcast it to other validators. Once it collects signatures from more than 2/3 of the validators, the block is confirmed.


[more about PoS](/docs/base/mapo-relay-chain/protocol/pos_en.md)

## block-anatomy

A block contains a lot of information. It includes the block header and the block body：

```golang

// Header represents a block header in the Ethereum blockchain.
type Header struct {
	ParentHash  common.Hash    `json:"parentHash"       gencodec:"required"`
	Coinbase    common.Address `json:"miner"            gencodec:"required"`
	Root        common.Hash    `json:"stateRoot"        gencodec:"required"`
	TxHash      common.Hash    `json:"transactionsRoot" gencodec:"required"`
	ReceiptHash common.Hash    `json:"receiptsRoot"     gencodec:"required"`
	Bloom       Bloom          `json:"logsBloom"        gencodec:"required"`
	Number      *big.Int       `json:"number"           gencodec:"required"`
	GasLimit    uint64         `json:"gasLimit"         gencodec:"required"`
	GasUsed     uint64         `json:"gasUsed"          gencodec:"required"`
	Time        uint64         `json:"timestamp"        gencodec:"required"`
	Extra       []byte         `json:"extraData"        gencodec:"required"`
	MixDigest   common.Hash    `json:"mixHash"`
	Nonce       BlockNonce     `json:"nonce"`

	// BaseFee was added by EIP-1559 and is ignored in legacy headers.
	BaseFee *big.Int `json:"baseFeePerGas" rlp:"optional"`
}

// Body is a simple (mutable, non-safe) data container for storing and moving
// a block's data contents (transactions and uncles) together.
type Body struct {
	Transactions   []*Transaction
	Randomness     *Randomness
	EpochSnarkData *EpochSnarkData
}

// Block represents an entire block in the Ethereum blockchain.
type Block struct {
	header         *Header
	randomness     *Randomness
	epochSnarkData *EpochSnarkData
	transactions   Transactions

	// caches
	hash atomic.Value
	size atomic.Value

	// Td is used by package core to store the total difficulty
	// of the chain up to and including the block.
	td *big.Int

	// These fields are used by package eth to track
	// inter-peer block relay.
	ReceivedAt   time.Time
	ReceivedFrom interface{}
}
```

## block-time

Block time refers to the time interval between two consecutive blocks. In `MAPO`, time is divided into units of 5 seconds, and a new block is produced every 5 seconds. Every 50000 blocks are considered an epoch, and each epoch involves a process of changing the set of validators.


## block-size

The last important note is that the size of blocks is bounded. Each block has a target size of 13 million gas units, but the block size can increase or decrease based on network demand until it reaches the block limit of 20 million gas units. The total gas consumed by all transactions in a block must be below the block's gas limit. This is important because it ensures that blocks cannot arbitrarily expand. If blocks could grow without limits, performance-challenged full nodes would gradually fall behind the network due to space and speed requirements. Larger blocks require more computational power to process them promptly in the next epoch. This is a centralizing factor that can be resisted by limiting block size.


## related-topics

- [Transactions](/docs/base/transactions/index_en.md)
- [Gas](/docs/base/gas/index_en.md)
- [Pos](/docs/base/mapo-relay-chain/protocol/pos_en.md)
