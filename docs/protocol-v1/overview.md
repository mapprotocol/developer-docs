# Protocol v1 Overview

## Introduction

Protocol v1 is MAP Protocol's light client-based cross-chain solution. It enables trustless cross-chain communication by verifying transactions through on-chain light clients, ensuring security based on the cryptographic proofs of source chains.

## Design Goals

1. **Trustless Verification**: No trusted third party required for cross-chain verification
2. **Cryptographic Security**: Security derived from source chain consensus
3. **Universal Compatibility**: Support for both EVM and non-EVM chains
4. **Decentralized Operation**: Permissionless participation as Maintainer or Messenger

## Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              Source Chain                                    │
│  ┌─────────────┐                                                            │
│  │   DApp      │──── Cross-chain Message ────┐                              │
│  └─────────────┘                             │                              │
│                                              ▼                              │
│  ┌─────────────────────────────────────────────────────────────────┐       │
│  │                    MOS Contract                                  │       │
│  │  - Emit cross-chain event                                        │       │
│  │  - Record message hash                                           │       │
│  └─────────────────────────────────────────────────────────────────┘       │
└─────────────────────────────────────────────────────────────────────────────┘
                                    │
                                    │  Maintainer monitors events
                                    │  Updates light client
                                    ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                            MAP Relay Chain                                   │
│  ┌─────────────────────────────────────────────────────────────────┐       │
│  │              Light Client (Source Chain)                         │       │
│  │  - Stores block headers                                          │       │
│  │  - Verifies Merkle proofs                                        │       │
│  └─────────────────────────────────────────────────────────────────┘       │
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────┐       │
│  │              Light Client (Target Chain)                         │       │
│  │  - Stores block headers                                          │       │
│  │  - Verifies Merkle proofs                                        │       │
│  └─────────────────────────────────────────────────────────────────┘       │
└─────────────────────────────────────────────────────────────────────────────┘
                                    │
                                    │  Messenger relays message
                                    │  with Merkle proof
                                    ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                              Target Chain                                    │
│  ┌─────────────────────────────────────────────────────────────────┐       │
│  │              Light Client (MAP Relay Chain)                      │       │
│  │  - Verifies proof from relay chain                               │       │
│  └─────────────────────────────────────────────────────────────────┘       │
│                                              │                              │
│                                              ▼                              │
│  ┌─────────────────────────────────────────────────────────────────┐       │
│  │                    MOS Contract                                  │       │
│  │  - Verify message proof                                          │       │
│  │  - Execute cross-chain call                                      │       │
│  └─────────────────────────────────────────────────────────────────┘       │
│                                              │                              │
│                                              ▼                              │
│  ┌─────────────┐                                                            │
│  │   DApp      │◄─── Cross-chain Message Delivered                          │
│  └─────────────┘                                                            │
└─────────────────────────────────────────────────────────────────────────────┘
```

## Core Components

### Light Client

Light clients are smart contracts deployed on each chain that maintain minimal state needed to verify transactions from other chains.

**Functions:**
- Store block headers (or validator set for PoS chains)
- Verify Merkle proofs
- Validate transaction inclusion

**Implementations:**
- MAPO Light Client (deployed on other chains)
- Other chain light clients (deployed on MAP Relay Chain)

### MOS (MAP Omnichain Service)

MOS is the message passing layer that standardizes cross-chain communication.

**Features:**
- Unified message format
- Cross-chain event emission
- Message verification interface

### Maintainer

Maintainers are off-chain services that keep light clients updated.

**Responsibilities:**
- Monitor source chain blocks
- Submit block headers to light clients
- Ensure light client state is current

### Messenger

Messengers relay cross-chain messages between chains.

**Responsibilities:**
- Monitor cross-chain events
- Generate Merkle proofs
- Submit messages with proofs to target chains
- Earn fees for successful delivery

## Cross-Chain Flow

### Step 1: Message Initiation

1. User/DApp calls MOS contract on source chain
2. MOS contract emits cross-chain event
3. Event includes: target chain, target address, payload, fee

### Step 2: Light Client Update

1. Maintainer detects new blocks on source chain
2. Maintainer submits block headers to light client on relay chain
3. Light client verifies and stores headers

### Step 3: Message Relay

1. Messenger detects cross-chain event
2. Messenger generates Merkle proof for the event
3. Messenger submits message + proof to target chain

### Step 4: Verification & Execution

1. Target chain MOS contract receives message
2. Light client verifies Merkle proof
3. If valid, MOS contract executes the cross-chain call
4. Target DApp receives the message

## Security Model

### Trust Assumptions

- Source chain consensus is secure
- Light client implementation is correct
- At least one honest Maintainer keeps light client updated

### Attack Vectors & Mitigations

| Attack | Mitigation |
|--------|------------|
| Fake block headers | Light client verifies consensus signatures |
| Invalid proofs | Merkle proof verification |
| Stale light client | Multiple independent Maintainers |
| Message replay | Nonce tracking in MOS contracts |

## Supported Chains

### EVM Chains
- Ethereum
- BSC
- Polygon
- Arbitrum
- And more...

### Non-EVM Chains
- Near Protocol
- (Others with light client implementation)

## Limitations

1. **Gas Costs**: Proof verification can be expensive
2. **Light Client Development**: Requires implementation for each chain
3. **Update Latency**: Light client needs time to sync
4. **No Bitcoin Support**: Bitcoin lacks smart contract capability

## Related Documentation

- [Light Client Design](./light-client/overview.md)
- [MOS Architecture](./mos/architecture.md)
- [Chain Integration Guide](./chains-integration/evm-chains.md)
