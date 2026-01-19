# Epoch

## Overview

An epoch is a fixed period of blocks in the MAP Relay Chain during which the validator set remains constant. At the end of each epoch, a new validator set is elected for the next epoch.

## Epoch Parameters

| Parameter | Value | Description |
|-----------|-------|-------------|
| Epoch Size | 50,000 blocks | Number of blocks per epoch |
| Block Time | ~5 seconds | Time between blocks |
| Epoch Duration | ~3 days | Approximate time per epoch |

## Epoch Lifecycle

### 1. Epoch Start

At the beginning of each epoch:
- The new validator set becomes active
- Validators begin producing blocks in round-robin fashion
- All pending votes from the previous epoch are processed

### 2. During Epoch

Throughout the epoch:
- The validator set remains fixed
- Validators take turns proposing blocks
- Transactions and smart contracts are executed
- Votes for the next epoch's validators can be cast

### 3. Epoch End

At the last block of each epoch:
- Epoch rewards are calculated and distributed
- The election contract is called to select the next validator set
- Validator voting state is reset (pending votes cleared)

## Validator Elections

During the generation of the last block in each epoch, the election contract selects the validator set for the next epoch:

1. **Vote Tallying**: The contract maintains a sorted list of each validator's locked MAPO votes (pending or activated)
2. **Selection**: Validators are selected based on the proportion of votes received
3. **Constraints**:
   - Minimum validators: 1
   - Maximum validators: 100
   - If minimum is not met, no changes are made to the validator set

## Epoch Rewards

At the end of each epoch, rewards are distributed to:
- **Validators**: For participating in block production and consensus
- **Voters**: For locking MAPO and voting for validators

The reward distribution is proportional to:
- Validator's participation in block production
- Amount of MAPO locked and voted

## Voting and Epochs

### Vote States

- **Pending Votes**: Votes cast during the current epoch, not yet activated
- **Active Votes**: Votes that have been activated and are earning rewards

### Vote Activation

- Pending votes are automatically activated at the end of the epoch when the validator is elected
- Voters can also manually activate their pending votes after the epoch in which they voted

### Vote Reset

Every epoch transaction resets the validator voting state:
- Any pending votes for adding/removing validators are reset
- This ensures a clean slate for each epoch's governance decisions

## Related Topics

- [Proof of Stake](./pos.md)
- [Istanbul BFT](./istanbul-bft.md)
- [Election](./election.md)
- [Rewards](./rewards.md)
