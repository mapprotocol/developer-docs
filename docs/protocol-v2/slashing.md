# Slashing Mechanism

## Overview

The slashing mechanism penalizes Maintainers who fail to fulfill their duties or act maliciously, ensuring the security and reliability of the cross-chain system. This document covers penalty scenarios, Slash Points, and Jail Epoch mechanisms.

## Penalty Types

| Type | Description |
|------|-------------|
| Slash Point | Accumulated penalty points affecting rewards and election priority |
| Jail Epoch | Imprisonment period during which nodes cannot participate in elections |
| Stake Slash | Direct deduction from staked MAPO |

## Penalty Scenarios

### Arbitrary Voting

When Maintainers vote on observations (TxIn, TxOut, KeyGen failures, etc.), they receive Slash Points to discourage arbitrary voting. Points are recovered when the vote reaches 2/3+ consensus.

### Vote Absence or Delay

When voting reaches 2/3+ consensus, Maintainers who haven't voted are penalized. Points can be recovered if the vote is submitted within the delay tolerance period after consensus.

### TSS KeyGen Failure

When the KeyGen process fails, nodes are penalized based on blame information:
- Current Maintainer members receive Slash Points
- Non-current Maintainers receive Jail Epochs

This penalty is not recoverable.

### TSS KeySign Failure

When KeySign fails, blamed nodes receive both Slash Points and Jail Epochs. This penalty is not recoverable.

### TSS Switching Timeout

When KeyGen success vote reaches consensus, Maintainers who haven't voted are penalized. Points can be recovered within the delay tolerance period.

### Migration Timeout

During asset migration, vote delays result in both Slash Points and Jail Epochs. Partial recovery is possible within the delay tolerance period.

### Asset Theft

If a transaction on the target chain doesn't match the initiated TxOutItem (wrong memo, mismatched data, or excessive gas), the system calculates the stolen value, converts it to MAPO, and applies a penalty multiplier.

If the slash amount exceeds the pause threshold, the affected chain is paused. Each Maintainer in the Vault is slashed proportionally based on their stake relative to total Vault stake.

## Slash Point Mechanism

### Effects on Rewards

Maintainer rewards are reduced based on accumulated Slash Points:

```
Reward = BaseReward × Max(0, EpochBlocks - SlashPoints) / EpochBlocks
```

When Slash Points exceed or equal EpochBlocks, the reward becomes zero.

### Effects on Election

During election, the system:
1. Filters out jailed nodes
2. Prioritizes removal of nodes with Slash Points exceeding the bad maintainer threshold
3. Sorts remaining candidates by stake amount

## Jail Epoch Mechanism

### Jail Effect

Jailed nodes cannot participate in elections until their Jail Epoch count reaches zero.

### Jail Release

On each TSS switching completion:
- All nodes' Jail Epoch is decremented by 1
- Nodes reaching zero can rejoin elections

### Unstaking While Jailed

When a Validator unstakes, MAPO is deducted for remaining Jail Epochs. The jail record is retained even after unstaking.

## Penalty Handling During TSS Switching

### On Churn Start

- Nodes with Jail Epoch > 0 are excluded from election
- Nodes exceeding the Slash Point threshold are prioritized for removal

### On Churn Complete

1. Jail Epoch decremented by 1 for all nodes
2. MAPO deducted based on Slash Points and consumed Jail Epochs
3. Slash Points cleared

## Parameter Configuration

### Slash Point Parameters

| Parameter | Description | Recommended |
|-----------|-------------|-------------|
| ObserveSlashPoint | Points on voting | 1 |
| LackOfObservationSlashPoint | Vote absence penalty | 2 |
| KeyGenFailSlashPoint | KeyGen failure penalty | 500 |
| KeySignFailSlashPoint | KeySign failure penalty | 10 |
| KeyGenDelaySlashPoint | KeyGen vote delay | 500 |
| MigrationDelaySlashPoint | Migration delay | 360 |

### Jail Epoch Parameters

| Parameter | Description | Recommended |
|-----------|-------------|-------------|
| KeyGenFailJailEpoch | KeyGen failure jail | 4 |
| KeySignFailJailEpoch | KeySign failure jail | 1 |
| MigrationDelayJailEpoch | Migration delay jail | 2 |

### Other Parameters

| Parameter | Description |
|-----------|-------------|
| ObserveDelayTolerance | Vote delay tolerance in blocks |
| BadMaintainerSlashPointThreshold | Threshold for bad node classification |
| MAPOPerSlashPoint | MAPO deducted per Slash Point |
| MAPOPerJailEpoch | MAPO deducted per Jail Epoch |
| PauseOnSlashThreshold | Threshold triggering chain pause |

## Penalty Summary

| Scenario | Slash Point | Jail Epoch | Recoverable |
|----------|-------------|------------|-------------|
| Arbitrary voting | +1 | - | Yes |
| Vote absence | +2 | - | Yes |
| KeyGen failure (member) | +500 | - | No |
| KeyGen failure (non-member) | - | +4 | No |
| KeySign failure | +10 | +1 | No |
| KeyGen vote delay | +500 | - | Yes |
| Migration delay | +360 | +2 | Yes |
| Asset theft | - | - | No (direct slash) |
