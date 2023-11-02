---
title: Gas Fees
description:
lang: en
---

Gas is crucial for the `MAPO-Relay-Chain` network. It's what makes it run, much like a vehicle needs gasoline. Here, `MAPO-Relay-Chain` is referred to as `MAPO`.

## prerequisites

To better understand this page, it is recommended to read about [transactions](/docs/base/transactions/index_en.md) and the [EVM](/docs/mapo-stack/compatible-evm/index_en.md) first.

## what-is-gas

`Gas` refers to the computational work required to perform specific actions on the `MAPO network`.

Since every transaction on `MAPO` requires computational resources to execute, each transaction has a cost associated with it. `Gas` is the term used to describe the fees required to perform transactions on `MAPO`, regardless of whether the transactions succeed or fail.

![cost Gas in the EVM](/docs/mapo-stack/compatible-evm/evm-gas.jpg) 

Essentially, Gas fees are paid in `MAPO`'s native cryptocurrency, `MAPO coins`. Gas prices are denoted in Gwei, with Gwei itself being a unit of MAPO coin - 1 Gwei is equal to 0.000000001 MAPO coin (10^-9 MAPO coin). For example, you can say your Gas cost is 1 Gwei, rather than saying your Gas cost is 0.000000001 MAPO coin. The term "gwei" itself represents "giga-wei," which is equal to 1,000,000,000 wei. Wei, in turn, is the smallest unit of MAPO coin, named after [Wei Dai](https://wikipedia.org/wiki/Wei_Dai), the inventor of [b-money](https://www.investopedia.com/terms/b/bmoney.asp).

## gas fee

The calculation of transaction fees in the `MAPO network` follows the same approach as `Ethereum's London upgrade`.

Let's say Jordan needs to pay Taylor 1 MAPO coin. In the transaction, the gas limit is set at 21,000 units, the base fee is 10 Gwei, and Jordan pays a 2 Gwei fee.

Now, the total cost is calculated as: `units of gas used * (base fee + priority fee)`, where the `base fee` is a protocol-defined value, and the `priority fee` is what the user sets as a fee for validators.

So, `21,000 * (10 + 2) = 252,000 Gwei` or 0.000252 MAPO coins.

When Jordan makes the transfer, 1.000252 MAPO coins will be deducted from Jordan's account. Taylor's account will increase by 1.0000 MAPO coins. The validators will receive a fee of 0.000042 MAPO coins, and 0.00021 MAPO coins of the base fee will be burned.

Additionally, Jordan can set a maximum fee (`maxFeePerGas`) for the transaction. Any difference between the maximum fee and the actual fee will be refunded to Jordan(`refund = max fee - (base fee + priority fee`). This allows Jordan to set a maximum amount to pay for the transaction without worrying about "overpaying" the base fee during execution.

### block-size

MAPO has introduced variable-sized blocks. The target size for each block is 13 million units of gas, but the block's size can increase or decrease based on network demand, with the limitation that it cannot exceed 20 million units of gas.

This means that if the block size exceeds the target block size, the protocol will increase the base fee for the next block. Similarly, if the block size is smaller than the target block size, the protocol will reduce the base fee. The adjustment of the base fee is proportional to the difference between the current block size and the target block size.[more about block](/docs/base/block/index_en.md)。

### base-fee

Every block has a base fee as the floor price. To be eligible for inclusion in a block, a gas fee quote must be at least equal to the base fee. The base fee is independent of the current block and is determined by previous blocks, making it easier for users to predict transaction fees. When a block is mined, its base fee is `burned` and taken out of circulation.

The base fee is calculated using a formula that compares the size of the previous block (the total amount of gas used in all transactions) to the target size. If it exceeds the target block size, the base fee for each block can increase by up to 12.5% at most. This exponential growth helps to keep block sizes economically sustainable in the long term.


### priority-fee

Since the base fee is burned, the priority fee (tip) incentivizes miners to include transactions in blocks. Without a tip, miners would find it economically viable to mine empty blocks because they would receive the same block reward. In regular circumstances, a small tip offers very minimal incentive for miners to include a low-value transaction. For transactions that need to be prioritized for execution within the same block, a higher tip should be offered, striving to outbid competing transactions.

### maxfee

To execute a transaction on the network, users can specify a maximum fee limit that they are willing to pay for the transaction. This optional parameter is called `maxFeePerGas`. To execute the transaction, the maximum fee must exceed the sum of the base fee and the tip (fee). After the transaction is completed, the user will receive a refund of the difference between the maximum fee and the sum of the base fee and tip.

## why-do-gas-fees-exist

In short, gas fees help ensure the security of the MAPO network. Every computation executed on the network incurs a charge, which prevents malicious actors from introducing spam to the network. To prevent unintended or malicious infinite loops or other computational waste in code, each transaction is required to set a limit on the number of computational steps it can take. The fundamental unit of computation is "gas".

While there are fee limits specified in transactions, any unused gas in a transaction is refunded to the user (i.e., `max fee - (base fee + tip)`).

## what-is-gas-limit

Gas limit refers to the maximum amount of gas you are willing to consume in a transaction. More complex transactions involving [smart contracts](/docs/mapo-stack/compatible-evm/index_en.md) require more computational work, and therefore, they need a higher gas limit compared to simple transfers. A standard MAPO coin transfer typically requires a gas limit of 21,000 units.

For example, if you set a gas limit of 50,000 units for a simple MAPO coin transfer, the EVM virtual machine will consume 21,000 units, and you will receive the remaining 29,000 units as a refund. However, if you set too little gas, such as a 20,000 unit gas limit for a simple MAPO coin transfer, the EVM virtual machine will consume 20,000 units of gas and attempt to complete the transaction but will fail. The EVM virtual machine will then roll back all changes, but because the miner has already done work worth 20k gas units, those units of gas are consumed.




