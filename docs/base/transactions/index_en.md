---
title: transactions
description: MAPO-Relay-Chain Transactions - How They Work, Their Data Structure, and How to Send Them via Applications.
lang: en
---


Transactions are instructions issued by accounts and cryptographically signed. Accounts initiate transactions to update the state of the `MAPO-Relay-Chain` network. The simplest transaction involves transferring `MAPO coins` from one account to another. In the following, `MAPO-Relay-Chain` is referred to as `MAPO`.


## prerequisites

To help you better understand this page, we recommend that you read about [accounts](/docs/base/accounts/index_en.md) and our [MAPO introduction](/docs/base/intro-to-mapo/index_en.md) first.


## whats-a-transaction

`MAPO` transactions refer to actions initiated by externally owned accounts, meaning they are managed by individuals rather than smart contracts. For example, if Bob sends 1 `MAPO` coin to Alice, Bob's account must decrease by 1 `MAPO` coin, while Alice's account must increase by 1 `MAPO` coin. Transactions result in changes to the state.

Transactions that alter the EVM state need to be broadcast to the entire network. Any node can broadcast a request to execute a transaction on the `MAPO virtual machine`; subsequently, validators execute the transaction and propagate the resulting state changes to other parts of the network.

Transactions come at a cost and must be included in a valid block. To keep this overview concise, we will introduce gas fees and validation separately.


The submitted transaction includes the following information:

- `from`: The sender's address, which will sign the transaction. This will be an externally owned account since contract accounts cannot send transactions.

- `recipient`: The receiving address (if it's an externally owned account, the transaction will transfer value; if it's a contract account, the transaction will execute the contract code).
- `signature`: The identifier of the sender. This signature is generated when the sender's private key signs the transaction, ensuring that the sender has authorized this transaction.
- `nonce`: An incrementing counter that represents the number of transactions from the account.
- `value`: The amount of MAPO coins (in Wei) transferred from the sender to the recipient (1 MAPO coin = 1e+18 Wei).
- `data`: An optional field that can include arbitrary data.
- `gasLimit`: The maximum amount of gas units the transaction can consume.
- `maxPriorityFeePerGas`: The highest price, as a fee, per unit of gas spent that is offered to validators.
- `maxFeePerGas`: The highest cost per unit of gas that the sender is willing to pay for the transaction (including the `baseFeePerGas` and `maxPriorityFeePerGas`).

`Gas` refers to the computation required by validators to process the transaction. Users must pay for this computation. The gasLimit and maxPriorityFeePerGas determine the maximum transaction fee paid to validators.
[about more Gas](/docs/base/gas/index_en.md)。


### the-data-field

The vast majority of transactions involve external accounts interacting with contracts. Contracts are written in the Solidity language and their `data` field is interpreted according to the Application Binary Interface (ABI) of the application.

The first four bytes specify the function to be called using the hash of the function name and its parameters. The remaining part of the call data represents the function's parameters.

## types-of-transactions

MAPO has several different types of transactions:

- Regular Transactions: These are transactions from one account to another.
- Contract Deployment Transactions: These are transactions without a "to" address, and the data field is used for contract code deployment.
- Contract Execution: These are transactions that interact with already deployed smart contracts. In this case, the `to` address is the address of the smart contract.

### on-gas

As mentioned, executing a transaction consumes [Gas](/docs/base/gas/index_en.md). A simple transfer transaction requires 21,000 gas units.

So, if Bob wants to send 1 MAPO coin to Alice with a `baseFeePerGas` of 100 Gwei and a `maxPriorityFeePerGas` of 10 Gwei, Bob needs to pay the following fees:

```
(100 + 10) * 21000 = 2,310,000 gwei
--或--
0.00231 MAPO
```
Bob's account will be debited by **1.00231 MAPO coins** (1 MAPO coin to Alice and 0.00231 MAPO coins as a transaction fee).

Alice's account will be credited with **+1.0 MAPO coin**.

The base fee will be burned, which is **-0.0021 MAPO coin**.

The validator will receive a fee of **0.000210 MAPO coin**.

Any interactions with smart contracts also require gas. Any unused gas from transactions will be refunded to the user's account.

## transaction-lifecycle

After a transaction is submitted, the following happens:

1. A transaction hash (txid) is generated cryptographically.
2. The transaction is then broadcast to the network and added to the pool of pending network transactions, which includes all other transactions waiting to be processed.
3. Validators must select your transaction and include it in a block for it to be validated and considered `successful`.
4. As time passes, the block containing your transaction will transition from a `pending` status to a `finalized` status, at which point it becomes `last finalized` .Through these stages, it can be further assured that your transaction has succeeded and is irreversible.

## related-topics

- [accounts](/docs/base/accounts/index_en.md)
- [EVM](/docs/mapo-stack/compatible-evm/index_en.md)
- [Gas](/docs/base/gas/index_en.md)
