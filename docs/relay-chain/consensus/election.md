# Election

This document describes the validator election and voting management on the Atlas chain.

## Staking

Atlas uses a proof-of-stake consensus mechanism. To participate in block generation on the Atlas network, you need to register as a validator. Currently, to become a validator, you need to lock 1,000,000 MAPO and vote for yourself. Each election sorts validators by the number of votes received and selects the top N validators.

## Updating Active Validator Set

After processing transactions and epoch rewards, the active validator set is updated by running an election in the last block of each epoch.

## Electing Validators

Validators must have at least a 0.001 proportion of total votes to be considered for election. Therefore, validators cannot have zero votes. This approach helps avoid burning MAPO and limits the number of voters to within 1000.

The number of active validators that can be selected has a minimum target (1) and a maximum cap (100). If the minimum target is not met, the election will be aborted, and no changes will be made to the validator set for that epoch.

**Example:** Currently there are four validators on the chain:

- 0x5d643dfb9ae372ce4fdbc80890156e2cd8290846
- 0xa53516d49a72019692ac69cb42641942597654f6
- 0x6acdc02223100189d82a958d888f54fa27d60e8a
- 0xea9efaa232a4567eac21c8c096f8bff84595a244

If for some reason we don't elect validators (number of valid validators is less than 1), we will continue using the above validators. If we select the latest set of validators (meaning the number of new validators is greater than 1 and less than 100), we will replace the above validators with the new validators.

## Unstaking

After successful staking (locking), you can unstake (unlock) if needed. 15 days after unstaking, you can redeem your MAPO to your account balance through the withdrawal operation.

## Implementation

The [Election](https://github.com/mapprotocol/atlas-contracts/blob/main/contracts/governance/Election.sol) contract manages locked MAPO voting and epoch rewards, and runs validator elections.

## Related Topics

- [Rewards](./rewards.md)
- [Epoch](./epoch.md)
- [Proof of Stake](./pos.md)
