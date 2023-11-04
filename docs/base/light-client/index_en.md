
# Light Clients

## What is a Light Client

The principle of a light client is to verify transactions and state on the blockchain in a lightweight way, without needing to fully download and store all blockchain data. It works based on these key concepts:

- Block header verification: A light client only syncs block header data from the blockchain, not full transaction data. Block headers contain important info about a block, like hash values, timestamps, difficulty target, etc. The light client verifies these block headers follow the blockchain's consensus rules.

- Merkle tree verification: To verify specific transactions or state, the light client can use Merkle tree structures. Merkle trees are binary tree structures where each leaf node contains the hash of a data block, and non-leaf nodes contain hashes of their child nodes. By comparing the hash of the target data with the tree root hash, the light client can verify data integrity.

- Transaction and state proofs: The light client can request proofs from full nodes on the blockchain to verify specific transactions or state. These proofs are typically based on Merkle trees, proving the existence and validity of the target data.

- Genesis block verification: The light client needs to verify the genesis block, to ensure the initial state of the chain is correct. This usually includes verifying the genesis block's signature and other metadata.

- Difficulty target verification: The light client also verifies the difficulty target of each block, to ensure the block meets consensus rules and was mined with proof of work.

- Reverse verification: The light client can make requests to full nodes on the network, to get required proofs and data. It can request data from multiple full nodes and verify consistency by comparing.

In summary, light clients work by relying on verifying block headers, using Merkle trees to verify data, requesting proofs and data, and reverse verification to ensure blockchain security and integrity. This allows light clients to verify blockchain transactions and state in a lightweight way, without requiring full node resources and storage.

## Light Clients vs SPV

Light clients and SPV (Simplified Payment Verification) are similar but not completely identical concepts for verifying transactions and state on blockchains. There is some overlap between them but also some differences:

Light Client:

- A lightweight blockchain node, typically does not download and store the entire blockchain data.

- Can verify transactions and state on the blockchain, but often selectively downloads and verifies block headers and partial transactions to verify transactions of interest.

- Can be used for more general operations including querying smart contract state, verifying on-chain events, etc.

SPV (Simplified Payment Verification):

- A concept in Bitcoin for lightweight method to verify bitcoin transactions.

- Allows wallet clients to verify their own transactions without downloading the entire bitcoin blockchain. Only block headers need to be downloaded and verified.

- Focuses on payment verification, typically used to verify confirmation of bitcoin transactions to ensure received payments are valid.

Relationships and Differences:

Similarities:

Both designed for lightweight verification on blockchains.
Both achieve verification by verifying block headers rather than all data of the entire blockchain.

Differences:

SPV is Bitcoin-specific terminology, mainly for payment verification. Focuses on verifying payment transactions, not smart contracts or more complex blockchain operations.

Light client is a more generic term, can be used for various blockchains including Ethereum, Polkadot, etc. Can perform more general operations like querying smart contract state, verifying on-chain events, etc.

In summary, light client is a broader concept that can be used for different blockchains and perform more functions, while SPV is a Bitcoin-specific term focused on payment verification. In the Bitcoin ecosystem SPV is a subset of light clients.

## MAPO Light Clients

MAPO light clients are mainly designed to verify the validity and legality of transactions. So MAPO light clients only need to implement `genesis block`, `block header verification`, and `MPT verification`, without needing to interact with full nodes.

- Genesis block: MAPO light clients can support a custom height as `genesis block`, instead of syncing from genesis height like normal light clients.

- Block header verification: MAPO light clients sync block header data, and verify block headers based on MAPO-Relay-Chain's [consensus mechanism](/docs/base/mapo-relay-chain/consensus/index.md).

- MPT verification: MAPO light client [MPT](/docs/base/mpt/index.md) verification is mainly used to verify transaction validity and legality. This is done primarily by verifying the `Receipt` corresponding to the transaction and the proof data of the `Receipt` MPT tree containing that `Receipt` in the block containing the transaction. This allows calculating a `ReceiptRoot` consistent with the `ReceiptRoot` in the block header.