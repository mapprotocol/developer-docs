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

权益证明是指：

- 验证节点必须质押1000000个MAPO币，作为抵押品防止发生不良行为。 这有助于保护网络，因为如果发生不诚实活动且可以证实，部分甚至全部质押金额将被销毁。
- 在每个时隙（5 秒的时间间隔）中，会随机选择一个验证者作为区块提议者。 他们将交易打包执行并签名，然后确定一个新的“状态”。 他们将这些信息包装到一个区块中并传送给其他验证者。
- 其他获悉新区块的验证者再次执行区块中包含的交易，确定他们同意对全局状态提出的修改。 假设该区块是有效的，验证者就将该区块签名并广播给其他验证者直到收集到超过2/3个验证者的签名，则该区块将被确认。


[有关权益证明的更多信息](/docs/base/mapo-relay-chain/index.md)

## 区块包含什么？ {#block-anatomy}

一个区块中包含很多信息。 区块中包含了区块头和区块体：

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

## 出块时间 {#block-time}

出块时间是指两个区块之间的时间间隔。 在MAPO中，时间划分为每 5 秒一个单位，每5秒将产生一个新的区块，每50000个区块视为一个纪元(epoch)，每个纪元将进行一次验证者集合的更换过程。


## 区块大小 {#block-size}

最后一条重要提示是，区块本身的大小是有界限的。 每个区块的目标大小为 1300 万单位燃料，但区块的大小将根据网络需求增加或减少，直至达到 2000 万单位燃料的区块限制。 区块中所有交易消耗的总燃料量必须低于区块的燃料限制。 这很重要，因为它可以确保区块不能任意扩大。 如果区块可以任意扩大，由于空间和速度方面的要求，性能较差的全节点将逐渐无法跟上网络。 区块越大，在下一个时隙中及时处理它们需要的算力就越强大。 这是一种集中化的因素，可以通过限制区块大小来抵制。


## 相关主题 {#related-topics}

- [交易](/docs/base/transactions/index.md)
- [燃料](/docs/base/gas/index.md)
- [权益证明](/docs/base/mapo-relay-chain/index.md)
