# Protocol v1 vs v2 Comparison

## Overview

MAP Protocol has two cross-chain solutions that serve different use cases and security models:

| Aspect | Protocol v1 | Protocol v2 |
|--------|-------------|-------------|
| **Verification Method** | Light Client | TSS + Light Client |
| **Security Model** | Cryptographic Proof | Threshold Signature + Cryptographic Proof |
| **Fault Tolerance** | Depends on light client | 2/3 Byzantine fault tolerance |
| **Supported Chains** | EVM & Non-EVM with light client support | All chains including Bitcoin |
| **Key Components** | Light Client, MOS, Compass | TSS, Vault, Gateway, Compass-TSS |

## Protocol v1: Light Client Solution

### Architecture

```
Source Chain                    MAP Relay Chain                 Target Chain
     │                               │                               │
     │  Cross-chain Event            │                               │
     ├──────────────────────────────►│                               │
     │                               │                               │
     │         Maintainer updates light client state                 │
     │                               │                               │
     │                               │  Messenger relays message     │
     │                               ├──────────────────────────────►│
     │                               │                               │
     │                               │  Light Client verifies proof  │
     │                               │                               │
```

### Key Features

- **Trustless Verification**: Uses light clients deployed on-chain to verify cross-chain messages
- **Cryptographic Security**: Security based on the consensus of source chain
- **Maintainer Role**: Updates light client state periodically
- **Messenger Role**: Relays cross-chain messages and proofs

### Components

1. **Light Client**: On-chain contract that maintains minimal blockchain state for verification
2. **MOS (MAP Omnichain Service)**: Message passing layer
3. **Compass**: Off-chain service running Maintainer and Messenger

### Limitations

- Requires light client implementation for each chain
- Higher gas costs for proof verification
- Limited support for chains without smart contracts (e.g., Bitcoin)

## Protocol v2: TSS + Light Client Fusion

### Architecture

```
Source Chain                    MAP Relay Chain                 Target Chain
     │                               │                               │
     │  Deposit to Vault             │                               │
     ├──────────────────────────────►│                               │
     │                               │                               │
     │         Maintainers observe and reach consensus               │
     │                               │                               │
     │                               │  TSS KeySign                  │
     │                               ├──────────────────────────────►│
     │                               │                               │
     │                               │  Gateway verifies TSS sig     │
     │                               │                               │
```

### Key Features

- **TSS Security**: 2/3 threshold signature ensures no single point of failure
- **Light Client Integration**: Can integrate light client for additional verification layer
- **Multi-chain Support**: Supports Bitcoin and other non-contract chains
- **Decentralized Custody**: Vault addresses managed by TSS, no centralized custody

### Components

1. **TSS (Threshold Signature Scheme)**: Distributed key generation and signing
2. **Vault**: Cross-chain asset custody address managed by TSS
3. **Gateway**: Contract on target chains for signature verification
4. **Maintainer Manager**: On-chain governance of Maintainer set
5. **Compass-TSS**: Off-chain service for TSS operations

### Advantages over v1

- Supports chains without smart contracts
- Lower gas costs (single signature verification vs. proof verification)
- More flexible security model
- Better Bitcoin integration

## When to Use Which

### Use Protocol v1 When:

- Cross-chain messaging between EVM chains
- Maximum trustlessness is required
- Light client already exists for both chains
- Gas cost is not a primary concern

### Use Protocol v2 When:

- Cross-chain with Bitcoin or other non-contract chains
- Asset transfers requiring custody
- Lower gas costs are important
- Need for LP-based liquidity

## Migration Path

Protocol v2 is designed to be compatible with v1. The roadmap includes:

1. **Phase 1**: v2 operates independently for Bitcoin and new chains
2. **Phase 2**: v2 integrates light client verification for enhanced security
3. **Phase 3**: Unified interface for both v1 and v2 features

## Security Comparison

| Security Aspect | v1 | v2 |
|-----------------|----|----|
| Verification | On-chain light client | TSS signature + optional light client |
| Trust Assumption | Source chain consensus | 2/3 of Maintainers honest |
| Attack Vector | Light client bugs | TSS key compromise (requires 1/3+) |
| Slashing | Limited | Full slashing mechanism |
| Recovery | Depends on light client | Vault migration via Churn |
