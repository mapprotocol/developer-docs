# MAP Protocol Architecture

## Overview

MAP Protocol is a peer-to-peer cross-chain infrastructure that enables secure and decentralized interoperability between heterogeneous blockchains. The architecture is designed with a three-layer structure.

## Three-Layer Architecture

```
┌─────────────────────────────────────────────────────────────────────────┐
│                        Application Layer                                 │
│                                                                          │
│    Omnichain DApps    │    Cross-chain Bridges    │    Other Apps       │
└─────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                     MOS (MAP Omnichain Service) Layer                    │
│                                                                          │
│    Message Passing    │    Asset Transfer    │    Data Verification     │
└─────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                         Protocol Layer                                   │
│                                                                          │
│    MAP Relay Chain    │    Light Clients    │    TSS Network            │
└─────────────────────────────────────────────────────────────────────────┘
```

### Layer 1: Protocol Layer

The foundation layer providing core cross-chain verification and consensus.

**Components:**

| Component | Description |
|-----------|-------------|
| MAP Relay Chain | EVM-compatible blockchain serving as the relay hub |
| Light Clients | On-chain verification of other chain's state |
| TSS Network | Threshold signature scheme for decentralized custody |
| Maintainers | Network participants maintaining cross-chain state |

### Layer 2: MOS (MAP Omnichain Service) Layer

The service layer providing cross-chain messaging and asset transfer capabilities.

**Components:**

| Component | Description |
|-----------|-------------|
| Message Protocol | Standard for cross-chain message format |
| Messenger | Off-chain service relaying messages |
| Vault/Gateway | Asset custody and verification contracts |

### Layer 3: Application Layer

The user-facing layer where omnichain applications are built.

**Examples:**

- Cross-chain DEX
- Omnichain NFT
- Cross-chain Lending
- Multi-chain Governance

## MAP Relay Chain

MAP Relay Chain (Atlas) is the core of MAP Protocol, serving as:

1. **Relay Hub**: Central point for cross-chain message routing
2. **Verification Center**: Hosts light clients for connected chains
3. **Governance Platform**: Manages validators, maintainers, and protocol parameters

### Consensus

- **Consensus Algorithm**: Istanbul BFT (IBFT)
- **Block Time**: ~5 seconds
- **Finality**: Instant finality with 2/3+ validator agreement
- **Staking**: Proof of Stake with MAPO token

### Key Features

- EVM compatible
- Precompile contracts for cryptographic operations
- Genesis contracts for staking and governance

## Cross-Chain Verification

MAP Protocol supports two verification methods:

### Light Client Verification (v1)

```
Source Chain              Relay Chain              Target Chain
     │                         │                        │
     │    Block Header         │                        │
     ├────────────────────────►│                        │
     │                         │                        │
     │    Merkle Proof         │    Verified Message    │
     ├────────────────────────►├───────────────────────►│
     │                         │                        │
```

### TSS Verification (v2)

```
Source Chain              Maintainer Network         Target Chain
     │                         │                        │
     │    Cross-chain Event    │                        │
     ├────────────────────────►│                        │
     │                         │                        │
     │                    TSS KeySign                   │
     │                         │                        │
     │                         │    TSS Signature       │
     │                         ├───────────────────────►│
     │                         │                        │
```

## Network Participants

### Validators

- Produce blocks on MAP Relay Chain
- Participate in IBFT consensus
- Stake MAPO tokens
- Can register as Maintainers

### Maintainers

- **v1**: Update light client state
- **v2**: Participate in TSS signing, observe cross-chain events

### Messengers

- Relay cross-chain messages
- Submit proofs to target chains
- Earn fees for message delivery

### Relayers (v2)

- Submit TSS signatures to target chains
- Can be Maintainers or independent operators

### LP Providers (v2)

- Provide liquidity to Vaults
- Earn cross-chain fees

## Supported Chains

MAP Protocol is designed to support:

- **EVM Chains**: Ethereum, BSC, Polygon, Arbitrum, etc.
- **Non-EVM Chains**: Near, Solana, TON
- **Bitcoin**: Via TSS-based Vault

## Security Model

### Decentralization

- No single point of failure
- Distributed validation and signing
- On-chain governance

### Cryptographic Security

- Light client verification based on source chain consensus
- TSS threshold signature (2/3 fault tolerance)
- Slashing for malicious behavior

### Economic Security

- Staking requirements for validators and maintainers
- Slashing penalties for misbehavior
- Incentive alignment through rewards
