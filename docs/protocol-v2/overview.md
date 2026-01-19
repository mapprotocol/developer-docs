# MAP Protocol v2.0 Overview

## Design Goals and Background

MAP Protocol v2.0 is a decentralized cross-chain protocol based on TSS (Threshold Signature Scheme). The protocol enables secure cross-chain asset transfers through threshold signature technology without relying on traditional multi-sig wallets or centralized custody solutions.

**Core Design Goals:**

- **Decentralized Security**: Through TSS threshold signatures, no single node can independently control cross-chain assets
- **High Fault Tolerance**: The system can tolerate up to 1/3 of nodes failing or acting maliciously
- **Multi-chain Support**: Supports EVM-compatible chains, Bitcoin, and other heterogeneous chains
- **On-chain Governance**: Maintainer management and state consensus implemented through Solidity contracts on MAP Relay Chain

## System Roles

### Validator

- Block producers and consensus participants of MAP Relay Chain
- Responsible for validating and confirming cross-chain transactions
- Participate in network consensus by staking MAPO tokens
- Validators can register as Maintainers to participate in cross-chain signing

### Maintainer

- Core participants in cross-chain signing
- Elected from registered Validators
- Responsibilities:
  - Monitor cross-chain events on source chains (Observer)
  - Participate in TSS key generation (KeyGen)
  - Participate in cross-chain transaction signing (KeySign)
  - Submit observation results to MAP Relay Chain
- Hold TSS private key shares, collectively managing Vault addresses

### Relayer

- Responsible for submitting signed transactions to target chains
- Receive transaction fee subsidies and rewards
- Can be Maintainer nodes or independent service providers
- For contract chains, anyone can submit signatures to the target chain Gateway

### LP Provider (Liquidity Provider)

- Provide liquidity to cross-chain pools
- Receive cross-chain fee sharing and LP incentives
- Participate by depositing assets to Vault addresses

## Overall Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              User/DApp                                       │
└─────────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                           Source Chain                                       │
│  ┌─────────────┐    ┌─────────────┐    ┌─────────────┐                      │
│  │  Gateway    │    │    Vault    │    │ Cross-chain │                      │
│  │  Contract   │───▶│   Address   │───▶│   Events    │                      │
│  └─────────────┘    └─────────────┘    └─────────────┘                      │
└─────────────────────────────────────────────────────────────────────────────┘
                                    │
                                    │ Observer Monitoring
                                    ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                        Maintainer Network                                    │
│  ┌─────────────┐    ┌─────────────┐    ┌─────────────┐    ┌─────────────┐   │
│  │ Maintainer  │    │ Maintainer  │    │ Maintainer  │    │ Maintainer  │   │
│  │     A       │◀──▶│     B       │◀──▶│     C       │◀──▶│     D       │   │
│  └─────────────┘    └─────────────┘    └─────────────┘    └─────────────┘   │
│         │                  │                  │                  │          │
│         └──────────────────┴──────────────────┴──────────────────┘          │
│                                    │                                         │
│                           P2P Network Communication                          │
│                           TSS KeyGen / KeySign                               │
└─────────────────────────────────────────────────────────────────────────────┘
                                    │
                                    │ Submit Observations/Votes
                                    ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                           MAP Relay Chain                                    │
│                                                                              │
│  ┌──────────────────┐    ┌──────────────────┐    ┌──────────────────┐       │
│  │   Maintainer     │    │   TSS            │    │   Relay          │       │
│  │   Manager        │    │   Manager        │    │                  │       │
│  │ Register/Elect/  │    │ TSS Generation/  │    │ Cross-chain      │       │
│  │ Incentive/Slash  │    │ Switching        │    │ Flow Management  │       │
│  └──────────────────┘    └──────────────────┘    └──────────────────┘       │
│                                                                              │
│  ┌──────────────────┐    ┌──────────────────┐    ┌──────────────────┐       │
│  │   Vault          │    │   Registry       │    │   Gas            │       │
│  │   Manager        │    │                  │    │   Service        │       │
│  │ State Transition/│    │ Chain/Token/     │    │ Fee Recording/   │       │
│  │ Asset Recording  │    │ Alias Registry   │    │ Updating         │       │
│  └──────────────────┘    └──────────────────┘    └──────────────────┘       │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
                                    │
                 ┌──────────────────┴──────────────────┐
                 │                                     │
                 ▼                                     ▼
┌────────────────────────────────┐    ┌────────────────────────────────┐
│   Contract Chains (EVM/Solana) │    │  Non-contract Chains (Bitcoin) │
│                                │    │                                │
│  TSS signature submitted to    │    │  TSS builds and signs tx       │
│  Relay Chain                   │    │         │                      │
│         │                      │    │         ▼                      │
│         ▼                      │    │  Broadcast to target chain     │
│  Relayer/Anyone submits sig    │    │         │                      │
│         │                      │    │         ▼                      │
│         ▼                      │    │  ┌─────────────┐               │
│  ┌─────────────┐               │    │  │   Vault     │               │
│  │  Gateway    │               │    │  │   Address   │               │
│  │  Verify sig │               │    │  │   (TSS)     │               │
│  │  Execute    │               │    │  └─────────────┘               │
│  │  TSS Switch │               │    │                                │
│  └─────────────┘               │    │                                │
└────────────────────────────────┘    └────────────────────────────────┘
```

## Core Components

### On-chain Components

**MAP Relay Chain Contracts:**

| Component | Function |
|-----------|----------|
| Maintainer Manager | Maintainer registration, updates, election, and incentive/slashing |
| TSS Manager | Manage TSS generation and switching processes |
| Relay | Manage cross-chain flow on Relay Chain |
| Vault Manager | Vault state transitions and asset recording per chain |
| Registry | Register and manage supported chains, tokens, and aliases |
| Gas Service | Record and update fee information for each chain |

**Other Chain Contracts:**

| Component | Function |
|-----------|----------|
| Gateway | Receive/send cross-chain assets, verify TSS signatures, complete TSS switching |

### Off-chain Components (Maintainer Nodes)

| Component | Function |
|-----------|----------|
| Observer | Monitor cross-chain events on each chain, parse and submit to MAP Relay Chain |
| Signer | Participate in TSS KeyGen and KeySign processes |
| P2P Network | Communication network between Maintainer nodes |
| Local Storage | Local storage for key shares, pending transactions, etc. |

## Vault State Model

Vault is an address managed by TSS, used to custody cross-chain assets. Each Vault has the following states:

```
┌──────────┐    Election Complete   ┌──────────┐    New Vault Activated  ┌──────────┐
│  Pending │ ─────────────────────▶ │  Active  │ ─────────────────────▶  │ Retiring │
│          │                        │          │                         │          │
└──────────┘                        └──────────┘                         └──────────┘
                                                                               │
                                                                               │ Asset Migration Complete
                                                                               ▼
                                                                         ┌──────────┐
                                                                         │ Retired  │
                                                                         │          │
                                                                         └──────────┘
```

| State | Description |
|-------|-------------|
| Active | Currently active Vault, receives new cross-chain deposits |
| Retiring | Vault being migrated, no longer receives new deposits, waiting for asset migration to complete |
| Retired | Vault with completed asset migration, no longer in use |

**Notes:**
- If users mistakenly transfer assets to a Retired Vault and the corresponding TSS nodes are still running, the system will automatically refund the assets to the user
- The system synchronizes all Vaults in Active and Retiring states
- Also synchronizes Retired Vaults generated within a certain number of recent blocks (for handling delayed transaction confirmations and refunds)

## TSS Threshold Signature

MAP Protocol v2.0 uses TSS (Threshold Signature Scheme) for decentralized asset custody.

**Basic Principles:**
- Private keys are split into multiple shares distributed across different Maintainer nodes
- Signing requires at least 2/3 of nodes to collaborate
- No single node can independently generate a valid signature

**Key Processes:**

| Process | Description |
|---------|-------------|
| KeyGen | Key generation, after election, new Maintainer set collaboratively generates new Vault public key |
| KeySign | Transaction signing, Vault Maintainer members collaborate to generate transaction signature |

**Supported Signature Algorithms:**

| Algorithm | Use Cases |
|-----------|-----------|
| secp256k1 | Bitcoin, EVM-compatible chains, and other chains supporting secp256k1 |

**Handling Chains Not Natively Supporting secp256k1:**
- For chains like Solana using ed25519, verification is completed by verifying secp256k1 signatures through on-chain contracts
- Gateway contracts are responsible for verifying TSS signature validity

## Glossary

| Term | Description |
|------|-------------|
| TSS | Threshold Signature Scheme |
| Maintainer | Core participant in cross-chain signing |
| Validator | Validator of MAP Relay Chain |
| Vault | Cross-chain asset custody address managed by TSS |
| KeyGen | TSS key generation process |
| KeySign | TSS transaction signing process |
| Churn | TSS switching/rotation process |
| Observer | Module that monitors cross-chain events on each chain |
| Gateway | Cross-chain contract on other chains |
| Epoch | Period of MAP Relay Chain |
| Slash Point | Penalty points |
| Jail Epoch | Imprisonment period count |
| TxIn | Inbound transaction (Source Chain → Relay Chain) |
| TxOut | Outbound transaction (Relay Chain → Target Chain) |
| Memo | Cross-chain information field for non-contract chains |
