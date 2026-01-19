# TSS (Threshold Signature Scheme) Overview

## Introduction

TSS (Threshold Signature Scheme) is the core cryptographic technology in Protocol v2 that enables decentralized custody of cross-chain assets. It allows a group of parties to jointly generate and use a cryptographic key without any single party ever holding the complete private key.

## Basic Concepts

### What is TSS?

TSS is a cryptographic protocol that:

1. **Distributed Key Generation (DKG)**: Multiple parties collaboratively generate a public/private key pair where each party only holds a "share" of the private key
2. **Threshold Signing**: A subset of parties (meeting the threshold) can collaboratively sign messages without reconstructing the full private key
3. **No Single Point of Failure**: No individual party can sign alone or reconstruct the private key

### Threshold Parameters

In MAP Protocol v2:

- **n**: Total number of Maintainers in the TSS group
- **t**: Threshold required for signing (typically 2/3 of n)
- **Shares**: Each Maintainer holds one key share

Example: With 10 Maintainers (n=10) and threshold t=7, any 7 or more Maintainers can produce a valid signature.

## TSS vs Multi-sig

| Aspect | TSS | Multi-sig |
|--------|-----|-----------|
| On-chain footprint | Single signature | Multiple signatures |
| Gas cost | Lower (one sig verification) | Higher (multiple verifications) |
| Privacy | Signers not revealed | Signers visible on-chain |
| Flexibility | Threshold can change off-chain | Requires on-chain update |
| Key management | Distributed generation | Each party has full key |

## Supported Algorithms

### secp256k1 (ECDSA)

Primary algorithm used for:
- Bitcoin
- Ethereum and EVM chains
- Most blockchain networks

### Handling Non-secp256k1 Chains

For chains using different curves (e.g., ed25519 for Solana):
- Gateway contracts verify secp256k1 signatures
- Provides unified TSS infrastructure across all chains

## Key Processes

### 1. KeyGen (Key Generation)

The process of generating a new TSS key pair:

```
┌─────────────────────────────────────────────────────────────────┐
│                        KeyGen Process                            │
│                                                                  │
│  Round 1: Each party generates random polynomial                 │
│           Broadcasts commitment to coefficients                  │
│                                                                  │
│  Round 2: Each party sends secret shares to others               │
│           Verifies received shares against commitments           │
│                                                                  │
│  Round 3: Each party computes their final key share              │
│           Group computes combined public key                     │
│                                                                  │
│  Result:  - Shared public key (Vault address)                   │
│           - Each party has unique private key share              │
│           - No party knows full private key                      │
└─────────────────────────────────────────────────────────────────┘
```

**Triggers for KeyGen:**
- Initial setup of Maintainer network
- Churn (Maintainer set change)
- Key refresh for security

### 2. KeySign (Transaction Signing)

The process of collaboratively signing a transaction:

```
┌─────────────────────────────────────────────────────────────────┐
│                        KeySign Process                           │
│                                                                  │
│  Input:   Message to sign (transaction hash)                    │
│           Set of signing parties (≥ threshold)                   │
│                                                                  │
│  Round 1: Each party generates signing nonce                     │
│           Broadcasts commitment                                  │
│                                                                  │
│  Round 2: Each party reveals nonce                               │
│           Computes partial signature                             │
│                                                                  │
│  Round 3: Partial signatures combined                            │
│           Final signature produced                               │
│                                                                  │
│  Output:  Valid ECDSA signature                                  │
│           Verifiable with public key                             │
└─────────────────────────────────────────────────────────────────┘
```

**KeySign Triggers:**
- Outbound cross-chain transaction
- Vault migration during Churn
- Emergency operations

### 3. Churn (Key Rotation)

The process of rotating to a new TSS key with a new Maintainer set:

```
┌─────────────────────────────────────────────────────────────────┐
│                        Churn Process                             │
│                                                                  │
│  Phase 1: Election                                               │
│           - New Maintainer set elected                           │
│           - Old set still active                                 │
│                                                                  │
│  Phase 2: KeyGen                                                 │
│           - New set performs KeyGen                              │
│           - New Vault address generated                          │
│                                                                  │
│  Phase 3: Migration                                              │
│           - Assets transferred from old to new Vault             │
│           - Old set signs migration transactions                 │
│                                                                  │
│  Phase 4: Activation                                             │
│           - New Vault becomes Active                             │
│           - Old Vault marked as Retiring then Retired            │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

## Security Properties

### Threshold Security

- **t-of-n Security**: Adversary must compromise ≥t parties
- **With t = 2n/3**: System tolerates up to n/3 malicious parties

### Key Share Security

Each key share is:
- Generated using verifiable secret sharing
- Never leaves the Maintainer's secure storage
- Useless without other shares

### Attack Resistance

| Attack | Mitigation |
|--------|------------|
| Key share theft | Need ≥t shares to sign |
| Rogue key attack | Verifiable key generation |
| Replay attack | Message includes unique identifiers |
| Man-in-the-middle | Authenticated P2P channels |

## Implementation Considerations

### Network Requirements

- **Reliable P2P**: All parties must communicate during signing
- **Low Latency**: Signing rounds require timely responses
- **Availability**: Offline parties delay signing

### Storage Requirements

- **Key Share**: Encrypted storage of local key share
- **Peer Info**: Public keys and addresses of other Maintainers
- **State**: Current signing sessions and pending operations

### Failure Handling

- **Timeout**: If party doesn't respond, signing fails
- **Retry**: Can retry with different party subset
- **Reporting**: Non-responsive parties accumulate slash points

## Related Documentation

- [Maintainer](./maintainer.md)
- [Cross-chain Flow](./cross-chain-flow.md)
- [Security](./security.md)
