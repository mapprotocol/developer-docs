# Cross-chain Flow

## Overview

MAP Protocol v2 supports various cross-chain operations including asset transfers, liquidity management, and asset migration. This document describes the flow mechanisms for each operation type.

## Cross-out Flow

Cross-out enables users to transfer assets from a source chain to a target chain.

### Flow Diagram

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                            Cross-out Flow                                    │
└─────────────────────────────────────────────────────────────────────────────┘

  User Initiates           Observer Monitors         Consensus Vote
      │                          │                        │
      ▼                          ▼                        ▼
┌──────────┐             ┌──────────┐             ┌──────────┐
│  Source  │────────────▶│  Build   │────────────▶│  2/3+    │
│  Deposit │             │ TxInItem │             │ Consensus│
└──────────┘             └──────────┘             └──────────┘
                                                        │
      ┌─────────────────────────────────────────────────┘
      │
      ▼
┌──────────┐             ┌──────────┐             ┌──────────┐
│ Generate │────────────▶│   TSS    │────────────▶│  Target  │
│ TxOutItem│             │  Sign    │             │  Execute │
└──────────┘             └──────────┘             └──────────┘
```

### Process Steps

**Step 1: User Initiates Cross-chain**

- **Contract chains (EVM)**: User calls Gateway.crossOut(), assets transfer to Vault, CrossOut event emitted with sender, receiver, token, amount, target chain, and memo
- **Non-contract chains (Bitcoin)**: User sends assets to Vault address with cross-chain instructions in memo/OP_RETURN

**Step 2: Observer Monitoring**

Maintainer Observer monitors source chain blocks/logs, filters Vault-related transactions, parses memo for cross-chain info, and builds TxInItem after sufficient confirmations.

**Step 3: Consensus Voting**

Each Maintainer submits observation to Relay contract. Request confirmed after 2/3+ consensus reached.

**Step 4: Generate Outbound Transaction**

Relay contract generates TxOutItem, calculates target chain gas fee, deducts cross-chain fee, and emits TxOut event.

**Step 5: TSS Signing**

- **Contract chains**: Maintainers coordinate TSS signing via P2P, submit signature to Relay Chain, Relayer submits to target Gateway
- **Non-contract chains**: Maintainers build native transaction, TSS sign, broadcast directly to target chain

**Step 6: Completion Confirmation**

Observer monitors target chain, detects outbound tx on-chain, submits ObservedTxOut, updates status to complete.

### Gas Handling

| Fee Type | Payer | Description |
|----------|-------|-------------|
| Source chain Gas | User | Paid when initiating transaction |
| Target chain Gas | Deducted from amount | Based on GasService estimate |
| Cross-chain fee | Deducted from amount | Incentivizes Maintainers and LPs |

## Cross-in Flow

Cross-in is the target chain receiving assets, the execution phase of cross-out.

### Contract Chain Cross-in

1. Relayer gets TSS signature from Relay Chain
2. Submits to target Gateway.executeIn() with TxOutItem data + signature
3. Gateway verifies signature, checks order uniqueness, transfers from Vault to target address

### Non-contract Chain Cross-in

1. Maintainers build and sign native transaction via TSS
2. Broadcast directly to target chain network

## Add Liquidity

LP Providers deposit assets to Vault to earn cross-chain fee returns.

### Flow

```
LP Provider Deposit ──▶ Observer Detects ──▶ 2/3+ Consensus ──▶ Mint LP Tokens
       │                      │                    │                  │
       ▼                      ▼                    ▼                  ▼
  Gateway.addLiquidity()   Parse memo         Vote confirm      Send to receiver
  or Vault transfer        (LP address)                         on MAP Relay Chain
```

### LP Token Properties

| Property | Description |
|----------|-------------|
| Minting location | MAP Relay Chain |
| Valuation | Shares based on deposited asset value |
| Returns | Cross-chain fee sharing |

## Remove Liquidity

LP Providers redeem LP tokens to withdraw original assets.

### Flow

```
Burn LP Tokens ──▶ Calculate Amount ──▶ TSS Sign ──▶ Transfer from Vault
      │                  │                 │               │
      ▼                  ▼                 ▼               ▼
  MAP Relay Chain   Generate TxOutItem  Maintainers    To LP address
                    (chain + address)   collaborate    on target chain
```

## Asset Migration

During TSS key rotation (Churn), assets migrate from old Vault to new Vault.

### Contract Chain Migration

Assets custodied in Gateway contracts only require TSS public key update, no actual transfer needed.

```
New Vault KeyGen ──▶ Old Vault Signs ──▶ Update Gateway ──▶ Complete
      │                    │                   │               │
      ▼                    ▼                   ▼               ▼
  Get new TSS key    Sign switch msg     updateTssKey()    Old Vault Retired
                     (key + epoch)       on each chain
```

**Advantages**: No asset transfer, one transaction per chain, no service interruption

### Non-contract Chain Migration

Assets held at TSS addresses require actual transfers to new Vault.

```
Query Old Vault ──▶ Batch Generate ──▶ TSS Sign ──▶ Observer Confirm ──▶ Complete
      │                  │                │              │                │
      ▼                  ▼                ▼              ▼                ▼
  Get all UTXOs/    Split into       Old Vault      Monitor tx        Old Vault
  balances          multiple txs     Maintainers    on-chain          Retired
```

### Migration Comparison

| Chain Type | Method | Description |
|------------|--------|-------------|
| EVM chains | Key switch | One tx updates Gateway |
| Solana | Key switch | One tx updates Gateway |
| Bitcoin | Asset transfer | Batch migrate UTXOs |
| Dogecoin | Asset transfer | Similar to Bitcoin |

## Refund Mechanism

When cross-chain fails, assets are refunded to users.

### Trigger Conditions

| Scenario | Description |
|----------|-------------|
| Execution failure | Gateway execution fails |
| Insufficient amount | Too low after gas deduction |
| Chain paused | Target chain unavailable |
| Retired Vault deposit | User mistakenly transfers to old Vault |

### Refund Flow

```
Detect Refund Need ──▶ Generate Refund Tx ──▶ TSS Sign ──▶ Execute ──▶ Confirm
       │                      │                  │            │           │
       ▼                      ▼                  ▼            ▼           ▼
  Failure/insufficient   Target = source    Maintainers   Submit to   Status =
                         Address = sender   collaborate   source chain Refunded
```

### Refund Fees

| Fee | Description |
|-----|-------------|
| Refund Gas | Deducted from refund amount |
| Original fee | Not refunded (resources consumed) |

## Failure Retry

### Contract Chains

Signatures can be resubmitted without cancelling original transaction. System monitors and resubmits with adjusted gas if needed.

### Non-contract Chains

Failed transactions may need RBF (Replace-By-Fee) or double-spend cancellation, then rebuild with higher fees.

### Retry Parameters

| Parameter | Description |
|-----------|-------------|
| Max retries | Configurable, default 3 |
| Interval | Exponential backoff |
| Gas adjustment | May increase on retry |

## Status Tracking

| Status | Description |
|--------|-------------|
| Pending | Submitted, awaiting confirmation |
| Observed | Observer detected |
| Voting | Consensus in progress |
| Signed | TSS signing complete |
| Executed | Target chain confirmed |
| Completed | Fully done |
| Refunding | Refund in progress |
| Refunded | Refund complete |
| Failed | Terminal failure |
