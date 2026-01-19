# Security Mechanisms

## Overview

MAP Protocol v2 security relies on multiple layers of protection including TSS threshold signatures, consensus mechanisms, penalty mechanisms, and emergency pauses. This document details various security measures and response strategies.

## Byzantine Fault Tolerance

### Basic Principle

The system is designed based on Byzantine Fault Tolerance (BFT) principles, tolerating up to 1/3 of nodes failing or acting maliciously:

| Scenario | Fault Tolerance |
|----------|-----------------|
| Node offline | Tolerate < 1/3 nodes offline |
| Malicious nodes | Tolerate < 1/3 malicious nodes |
| Network partition | Tolerate < 1/3 nodes disconnected |

### TSS Signature Security

- **KeyGen**: Requires all participants to collaborate
- **KeySign**: Requires 2/3+ nodes to participate
- Any coalition of < 1/3 nodes cannot independently generate valid signatures

### Consensus Security

All critical operations require 2/3+ Maintainer confirmation:
- ObservedTxIn / ObservedTxOut
- TssPoolUpdate
- Any coalition of < 1/3 nodes cannot forge consensus

### Election Restrictions

During each TSS switch, no more than 1/3 of members can be removed from the original Maintainer set. This ensures sufficient overlap between old and new sets, preventing security risks from replacing too many nodes at once.

## Asset Theft Handling

### Detection Mechanism

Observers detect abnormal transactions by checking:
- Memo doesn't match TxOutItem
- Transfer amount doesn't match expected
- Target address doesn't match expected
- Gas consumed exceeds maximum allowed

### Penalty Calculation

The stolen token value (including gas fees) is converted to MAPO value and multiplied by a penalty coefficient greater than 1. The penalty is distributed proportionally among Vault Maintainers based on their stake.

### Automatic Pause

When the penalty amount exceeds the configured threshold, the affected chain is automatically paused to prevent further losses.

## Pause Mechanism

### Pause Types

| Type | Trigger | Impact |
|------|---------|--------|
| Chain pause | Asset theft, chain anomaly | All cross-chain operations for that chain |
| Global pause | System-level security event | All cross-chain operations |
| Token pause | Token contract anomaly | Cross-chain operations for that token |

### Behavior During Pause

- New cross-chain requests are rejected
- In-progress transactions continue execution
- Observer continues monitoring for post-recovery handling
- Users can initiate refund requests

## Replay Attack Protection

### Order ID Mechanism

Each cross-chain transaction has a unique Order ID derived from source chain, transaction hash, and log index. The system checks if an order has been executed before processing and marks it as executed afterward.

### Nonce/Epoch Mechanism

Operations requiring multiple signatures (like TSS key updates) include epoch numbers that must exceed the current epoch, preventing replay of old signatures.

## Signature Security

### Signature Verification

Gateway contracts verify TSS signatures by:
1. Recovering the public key from the signature
2. Verifying it matches the current TSS address

### Message Format

Signature messages include context information to prevent cross-chain replay:
- Target chain ID
- Order ID
- Receiver address
- Token address
- Amount
- Optional nonce

## Key Security

### Private Key Share Protection

| Measure | Description |
|---------|-------------|
| Local encrypted storage | Private key shares encrypted with password |
| Memory protection | Private keys not written to disk during runtime |
| Secure deletion | Private keys cleared from memory on exit |

### Key Rotation

TSS switching provides key rotation:
- Generate new TSS key pair periodically
- Old key shares become invalid
- Reduces key leakage risk
- Recommended forced rotation every N epochs

## Network Security

### P2P Communication

Maintainer inter-communication uses:
- TLS/Noise protocol encryption
- Node identity verification via ed25519 public key
- Message signing to prevent tampering
- Protection against man-in-the-middle attacks

### RPC Security

Chain RPC access employs:
- Multiple RPC nodes
- Cross-validation of responses
- Prevention of single point of failure
- Protection against RPC spoofing

## Economic Security

### Staking Requirement

Maintainers must stake sufficient MAPO as:
- Collateral for malicious behavior penalties
- Incentive for honest behavior
- Stake amount affects election priority

### Penalty Deterrence

The penalty system ensures malicious behavior is economically irrational:
- Slash Points affect rewards and election
- Jail Epochs prohibit election participation
- Stake slashing directly deducts from stake
- Penalty amounts exceed potential malicious gains

## Emergency Response

### Response Flow

```
Detect Anomaly ──▶ Assess Impact ──▶ Emergency Pause ──▶ Investigate
      │                 │                  │                 │
      ▼                 ▼                  ▼                 ▼
  Observer/         Determine          Pause chain/      Root cause
  Community/        scope and          Global pause      analysis
  Monitoring        severity

                           │
      ┌────────────────────┴────────────────────┐
      ▼                                         ▼
┌───────────┐                            ┌───────────┐
│ Remediate │                            │  Restore  │
│           │                            │  Service  │
└───────────┘                            └───────────┘
      │                                         │
      ▼                                         ▼
  Penalize nodes                           Remove pause
  Upgrade contracts                        Process backlog
  Fix vulnerabilities                      Post-incident review
```

### Emergency Contact

- Admin multisig address for emergency operations
- Core team 24/7 response capability

## Error Handling

| Scenario | Cause | Handling |
|----------|-------|----------|
| RPC issues | Node unavailable | 2/3 normal nodes sufficient |
| Insufficient balance | Gas fee changes | 2/3 normal sufficient |
| Swap amount too low | Insufficient after fees | Refund with fee deduction |
| Gas limit too small | Estimation issue | Set larger gas limit |
| Receiving address anomaly | Transfer failed | Fault tolerant transfer to vault |
| Duplicate processing | N/A | Order ID prevents duplicates |

## Supported Chains

### Contract Chains

| Chain | Chain ID | Alias | Status |
|-------|----------|-------|--------|
| Ethereum | 1 | Eth | Supported |
| BSC | 56 | Bsc | Supported |
| Polygon | 137 | Pol | Supported |
| Arbitrum | 42161 | Arb | Supported |
| Optimism | 10 | Op | Supported |
| Base | 8453 | Base | Supported |
| Linea | 59144 | Linea | Supported |
| Solana | 501 | Sol | Supported |
| Tron | 728126428 | Tron | Supported |
| MAP Protocol | 22776 | Mapo | Supported |

### Non-contract Chains

| Chain | Chain ID | Alias | Status |
|-------|----------|-------|--------|
| Bitcoin | 0 | Btc | Supported |
| Dogecoin | 3 | Doge | Planned |
| Litecoin | 2 | Ltc | Planned |
| XRP Ledger | 144 | Xrp | Planned |
