---
title: Bitcoin layer2 for Map Protocol
description: The implementation logic of MAP Protocol as a Bitcoin Layer 2。
lang: en
---



## BTC layer2

Similar to Layer 2 solutions within the Ethereum ecosystem, Bitcoin Layer 2 also focuses on bringing more use cases to the ecosystem and reducing transaction fees. Common Bitcoin Layer 2 solutions such as off-chain networks, centralized sidechains, and federated sidechains provide improved scalability and privacy protection for the Bitcoin network, although they are still somewhat behind compared to the Ethereum ecosystem. In May 2023, an innovative experiment called BRC-20 was implemented based on the [Ordinals protocol](https://docs.ordinals.com/), aimed at testing whether the Ordinals protocol could enhance the fungibility of Bitcoin, similar to the effect of issuing ERC-20 tokens on the Ethereum network. This experiment showcased the potential for Bitcoin interoperability.

As a peer-to-peer, cross-chain interoperability infrastructure, MAP Protocol has always been focused on cross-chain interoperability. Now, MAP Protocol is also serving as a Bitcoin Layer 2. On one hand, it leverages the Bitcoin network to enhance the security of its MAP Protocol network. On the other hand, it uses its cross-chain technology to further enrich the Bitcoin ecosystem, providing a new trading experience for the BRC-20 community.


### Ensuring the security of MAP Protocol through the Bitcoin network further

Bitcoin, with its immense computational power, can be considered a natural source of trust and serves as a `timestamp server` supported by proof of work. It provides an irreversible time order for events. In its native application, events involve various transactions executed on the Bitcoin ledger. In current applications aimed at enhancing the security of other blockchains, Bitcoin can also be used to timestamp events occurring in other blockchains. Each such event triggers a transaction sent to miners, who subsequently insert it into the Bitcoin ledger, thus timestamping the event. Transactions that timestamp events are referred to as `checkpoints`.

`Checkpoints` can be implemented using the `OP_RETURN` opcode of Bitcoin, which allows the publication of arbitrary 80-byte data in unspendable Bitcoin transactions. Each checkpoint must contain at least the hash of the PoS block to be checked (32 bytes) and a signature finalizing that block (32 bytes each). Here, the hash is used to identify the PoS block being checkpointed, and the signature is required to prevent adversaries from sending arbitrary hashes and pretending to checkpoint PoS blocks on Bitcoin.

A PoS chain can enhance its security and address the [long-range attack](https://medium.com/@abhisharm/understanding-proof-of-stake-through-its-flaws-part-3-long-range-attacks-672a3d413501) problem by utilizing the Bitcoin timestamp service's features. The `MAPO` platform regularly (every epoch) submits the `hash` and `signature` of the last block of each epoch as a `checkpoint` to the Bitcoin network. These `checkpoints` consist of the hash of the block and a single aggregated `BLS signature`, corresponding to the signature of the 2/3 set of validators who signed the block for finality, as well as the `epoch number` and `bitmap number`. As a result, `MAPO` clients can determine the final canonical chain of the `MAPO` platform's PoS chain by retrieving checkpoints from the Bitcoin network, thus protecting against [long-range attacks](https://medium.com/@abhisharm/understanding-proof-of-stake-through-its-flaws-part-3-long-range-attacks-672a3d413501) by malicious validators on the MAPO network.

![架构图](./frame1.png) 


### MAP Relay Chain with Bitcoin Network Checkpoints

The `MAP relay chain` features an independent set of validators responsible for maintaining the security and stability of the `MAP relay chain`. Each validator verifies and signs each newly generated block to ensure its correctness and legitimacy. Blocks with signatures from at least 2/3 of the validators are considered valid and will be finally confirmed and stored on the chain.

The validators will sign the following data:

**hash**: The hash of the block header without additional signature information.

**round**: The sequence number at which the validators reach consensus.

**commit**: Constant data.

The `signature data (Msg)` includes the `hash` of the block header, the sequence number at which validators reach consensus (`round`), and a `MsgCommit`, as follows:

```golang
// hash: header.Hash()
func PrepareCommittedSeal(hash common.Hash, round *big.Int) []byte {
	var buf bytes.Buffer
	buf.Write(hash.Bytes())
	buf.Write(round.Bytes())
	buf.Write([]byte{byte(istanbul.MsgCommit)})
	return buf.Bytes()
}
```

After signing the data and collecting signatures from other validators, the signature data is aggregated and used to construct an `IstanbulExtra` structure with relevant data, as follows:

```golang
type IstanbulExtra struct {
	// AddedValidators are the validators that have been added in the block
	AddedValidators []common.Address
	// AddedValidatorsPublicKeys are the BLS public keys for the validators added in the block
	AddedValidatorsPublicKeys []blscrypto.SerializedPublicKey
	// AddedValidatorsG1PublicKeys are the BLS public keys for the validators added in the block
	AddedValidatorsG1PublicKeys []blscrypto.SerializedG1PublicKey
	// RemovedValidators is a bitmap having an active bit for each removed validator in the block
	RemovedValidators *big.Int
	// Seal is an ECDSA signature by the proposer
	Seal []byte
	// AggregatedSeal contains the aggregated BLS signature created via IBFT consensus.
	AggregatedSeal IstanbulAggregatedSeal
	// ParentAggregatedSeal contains and aggregated BLS signature for the previous block.
	ParentAggregatedSeal IstanbulAggregatedSeal
}

type IstanbulAggregatedSeal struct {
	// Bitmap is a bitmap having an active bit for each validator that signed this block
	Bitmap *big.Int
	// Signature is an aggregated BLS signature resulting from signatures by each validator that signed this block
	Signature []byte
	// Round is the round in which the signature was created.
	Round *big.Int
}
```

The data from the `IstanbulExtra` structure is encoded using RLP and stored in the `header's Extra` field. This field includes the consensus `round` data, the BLS signature data (`AggregatedSeal.Signature`), and the changing set of validators.[more](https://docs.mapprotocol.io/develop/map-relay-chain/consensus/aggregatedseal)

#### CheckPoint

The MAP Protocol periodically constructs blocks on the relay chain into a `checkpoint` and submits it to the Bitcoin network to ensure the determinism of the entire relay chain, preventing it from being forged or overturned. The `checkpoint` needs to include:：

+ PreCheckPointHash: The hash of the previous checkpoint.

+ Root: The root hash used to ensure the state of the relay chain at that time.
  
+ Height: The block height on the relay chain corresponding to the `checkpoint`.

**Publishing the relay chain block hash to the Bitcoin network**：

+ Make Checkpoint: The relay chain periodically generates `checkpoint` information for the chain. It constructs a `root hash` that includes the `aggregated signatures` of validators at a specified `height` and the hash of the previous checkpoint confirmed over time.

+ OP_RETURN Tx: The relay chain will construct the generated checkpoint information into an OP_RETURN transaction.


**Checkpoint Time Sequence**：
  
+ The relay chain will utilize its `btc-light-client` to ensure the time ordering of checkpoint transactions. It will guarantee that the timestamps of Bitcoin checkpoints are incremental during checkpoint verification. This can be achieved by checking if the timestamp of a newly received checkpoint is greater than the timestamp of the previously received checkpoint, while also satisfying a specified `checkpoint-threshold`. If the timestamp is decreasing or equal, the checkpoint is rejected.

+ The relay chain also needs to perform checks based on the time of the generated checkpoints on itself and the time range of checkpoints on the Bitcoin Layer 1. This is crucial for maintaining consistent security practices and preventing potential long-range attacks.
  

**Verify Checkpoint**：

+ The relay chain client checks the `OP_RETURN outputs` in Bitcoin transactions, extracts checkpoint information, including hash, height, and so on.
  
+ Based on the checkpoint information at the corresponding height, the relay chain verifies whether the local data information is consistent. The relay chain obtains the corresponding validator set for the respective height and verifies whether the signature corresponding to the hash in the checkpoint is consistent. This process helps determine whether the relay chain has been attacked.


overall structure: 

![架构图](./frame4.jpg) 

### Enriching the Bitcoin ecosystem and enhancing the liquidity of BRC-20 assets.

The `MAPO` platform supports the cross-chain transfer of [inscription](https://docs.ordinals.com/inscriptions.html) assets (BRC-20) from the Bitcoin network to the `MAPO` platform in a peer-to-peer manner. This enables other cryptocurrencies on different blockchains to be traded with `BRC-20` assets through a more convenient and cost-effective route, enhancing the liquidity of [inscription](https://docs.ordinals.com/inscriptions.html)  assets. This interoperability helps expand the use cases of Bitcoin and integrates the Bitcoin ecosystem into a broader crypto financial ecosystem, bringing new contributions to the Bitcoin community.


`MAPO` provides a comprehensive solution that allows users to easily transfer `BRC-20` assets to the `MAPO` platform. `MAPO` utilizes the native [mapo-brc201](./brc201.md) protocol on the Bitcoin network, enabling users to seamlessly move assets from the `BRC-20` protocol to the BRC201 protocol and cross-chain to the `MAPO` platform without any loss. As shown in the diagram: 

![架构图](./frame2.png) 

+ Indexer Service: The Indexer service is primarily responsible for gathering and parsing `inscription transactions` on the Bitcoin network. It collects information related to these transactions, making them accessible for further analysis and processing.

+ Collection Service: Building on the data obtained from the Indexer service, the Collection service is responsible for analyzing and processing data related to `BRC-20` and [BRC-201](./brc201.md) protocols. It focuses on identifying and saving relevant user assets associated with these protocols.

+ Bridge Service: The Bridge service plays a crucial role in routing `BRC-20` assets from the Bitcoin network to the MAPO platform using the [BRC-201](./brc201.md) protocol. This service facilitates the seamless transfer of assets between the two networks, enhancing interoperability.

+ Order Service: The Order service is designed to support the creation and management of `inscription transactions` based on the [BRC-201](./brc201.md) protocol. It helps users construct and execute transactions that adhere to this protocol.

These services collectively contribute to the smooth operation and functionality of the MAPO platform, allowing for the transfer and management of assets across the Bitcoin network and the MAPO ecosystem.