---
title: MAPO Account
description: Explanation of MAPO Accounts - Their Data Structure and Relationship with Key Pair Cryptography。
lang: en
---


A `MAPO` account is an entity with a balance of `MAPO`, the main network coin of the [MAPO-Relay-Chain](/docs/base/mapo-relay-chain/index_en.md). These accounts can send transactions on the [MAPO-Relay-Chain](/docs/base/mapo-relay-chain/index_en.md). Accounts can be controlled by users or deployed as smart contracts. It's important to note that in this context, `MAPO` specifically refers to the native cryptocurrency of the `MAPO-Relay-Chain`.


## prerequisites

The topic of accounts is quite suitable for beginners. However, to help you better understand this page, we recommend that you first read our Introduction to [MAPO](/docs/base/intro-to-mapo/index_en.md).


## #types-of-account

`MAPO` has two types of accounts:

- Externally Owned Accounts (EOA): These accounts are controlled by anyone who possesses the private key. They are typical user accounts.

- Contract Accounts: These are accounts created by deploying smart contracts onto the network. They are controlled by the code of the smart contract. You can learn more about smart contracts [here](/docs/mapo-stack/compatible-evm/index_en.md).

Both of these account types can:

- Receive, hold, and send MAPO coins and tokens.
- Interact with deployed smart contracts.

### key-differences

**External Ownership**

- Account creation is free.
- Can initiate transactions.
- Externally Owned Accounts (EOAs) can only engage in MAPO coin and token transactions among themselves.
- Comprises a pair of cryptographic keys: a public key that controls the account's activities and a private key.

**Contracts**

- Creating contracts comes with a cost as it requires network storage space.
- They can only send transactions in response to receiving transactions.
- Transactions initiated from external accounts to contract accounts can trigger code execution capable of various operations, such as token transfers or even creating new contracts.
- Contract accounts do not have private keys. Instead, they are controlled by the logic of smart contract code.

## an-account-examined

MAPO accounts have four fields:

- `Nonce`: A counter that displays the number of transactions sent by an external account or the number of contracts created by a contract account. Each account can only execute a transaction with a given nonce once to prevent replay attacks. A replay attack refers to the broadcasting and repeated execution of a signed transaction.

- `Balance`: The amount of Wei that this address holds. Wei is the unit of measurement for MAPO coins, with each MAPO coin equal to 1e+18 Wei.

- `CodeHash`: This hash represents the account's code on the Ethereum Virtual Machine (EVM). Contract accounts have code snippets that can perform various operations when invoked by a message call. Unlike other account fields, the codeHash cannot be changed. All code snippets are stored under the corresponding hash in the state database for later retrieval. This hash is known as codeHash. For external accounts, the codeHash field is the hash of an empty string.

- `StorageRoot`: Also known as the storage hash. The 256-bit hash of the root node of the Merkle Patricia trie encodes the account's storage content (a mapping of 256-bit integer values) and is encoded as a Trie. It serves as a mapping from 256-bit Keccak hashes to integer keys and 256-bit integer values encoded for RLP. By default, this trie is empty for the account's storage content.

![chart of the account](./account.jpg) 

## externally-owned-accounts-and-key-pairs

An account is made up of an encrypted key pair, consisting of a public key and a private key. They help prove that transactions are genuinely signed by the sender and prevent forgery. Your private key is the key you use to sign transactions, ensuring that you have control over the funds associated with your account. You never truly "hold" cryptocurrencies; you hold the private key - the funds are always recorded in the ledger on the `map-relay-chain`.

This prevents malicious participants from broadcasting fake transactions because you can always verify the sender of a transaction.

If Alice wants to send MAPO coins from her account to Bob's account, she needs to create a transaction request and send it for validation. The use of public key encryption by the `map-relay-chain` ensures that Alice can prove that she originally initiated the transaction request. Without encryption mechanisms, a malicious adversary like Eve could simply broadcast a request that looks like "Send 5 MAPO coins from Alice's account to Eve's account," and no one could confirm that it didn't originate from Alice.

## account

When you want to create an account, most libraries will generate a random private key.

The private key consists of 64 hexadecimal characters and should be securely stored using encryption. For example:

`2851c4480f425bc4bd65bc673fc08683efe844d83bb5ae9a04e9566af5bbe2a1`

From the private key, a public key is generated using the [elliptic curve digital signature algorithm](https://wikipedia.org/wiki/Elliptic_Curve_Digital_Signature_Algorithm). To obtain the public address for an account, you can take the last 20 bytes of the Keccak-256 hash of the public key and prepend `0x`.

You can derive the public key from your private key, but you cannot obtain the private key from the public key. This means that keeping your private key secure is of utmost importance, as the name suggests: **PRIVATE**.

You need a private key to sign messages and transactions, which produces a signature. Others can then use this signature to obtain your public key, verifying the authorship of the information. In your application, you can use JavaScript libraries to send transactions over the network.

## contract-accounts

Contract accounts also have a 42-character hexadecimal address, for example:

`0x3CC4f7fB1C8edD8E37A6dD56456911fae41538cc`

The contract address is typically provided when deploying a contract to the `map-relay-chain` blockchain. The address is derived from the creator's address and the number of transactions sent from the creator's address, often referred to as the "nonce."

## validators-keys

MAPO also has another type of key known as `BLS` keys, which are used to identify validators and achieve consensus on the state of new blocks during the consensus process. These keys can be efficiently aggregated, reducing the bandwidth required to reach consensus on the network. Without this key aggregation, the minimum stake required for validators would be significantly higher.

[More information about validator keys](/docs/base/mapo-relay-chain/protocol/pos_en.md)。

## a-note-on-wallets

Accounts and wallets are different. An account refers to the key pair of the `MAPO` account owned by the user. A wallet, on the other hand, is an interface or application that allows you to interact with `MAPO` accounts.


## related-topics

- [smart contract](/docs/mapo-stack/compatible-evm/index_en.md)
- [Transaction](/docs/base/transactions/index_en.md)
