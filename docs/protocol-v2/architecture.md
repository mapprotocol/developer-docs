# Protocol v2 Architecture

## Overview

Protocol v2 introduces TSS (Threshold Signature Scheme) based cross-chain infrastructure, enabling secure asset transfers across heterogeneous chains including Bitcoin. This document describes the overall architecture and component interactions.

## System Architecture

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

## Component Details

### On-Chain Components (MAP Relay Chain)

#### Maintainer Manager

Manages the lifecycle of Maintainers:

- **Registration**: Validators can register as Maintainers
- **Election**: Periodic election of active Maintainer set
- **Incentives**: Distribution of cross-chain fee rewards
- **Slashing**: Penalty for malicious or offline behavior

#### TSS Manager

Manages TSS key generation and switching:

- **KeyGen Coordination**: Triggers and monitors KeyGen process
- **Vault Registration**: Records new Vault addresses
- **Churn Process**: Coordinates TSS switching during Maintainer set changes

#### Vault Manager

Manages Vault state across all chains:

- **State Tracking**: Active, Retiring, Retired states
- **Asset Recording**: Tracks assets in each Vault
- **Migration**: Coordinates asset migration during Churn

#### Relay

Manages cross-chain transaction flow:

- **TxIn Processing**: Records inbound cross-chain transactions
- **TxOut Scheduling**: Queues outbound transactions for signing
- **Status Tracking**: Monitors transaction completion

#### Registry

Central registry for protocol configuration:

- **Chain Registry**: Supported chains and their parameters
- **Token Registry**: Supported tokens and mappings
- **Alias Registry**: Human-readable names for addresses

#### Gas Service

Manages cross-chain gas fees:

- **Fee Calculation**: Determines fees for each chain
- **Fee Updates**: Allows dynamic fee adjustment
- **Fee Collection**: Records collected fees

### On-Chain Components (Other Chains)

#### Gateway

Entry/exit point for cross-chain operations:

- **Deposit Handling**: Receives user deposits
- **Signature Verification**: Verifies TSS signatures
- **Withdrawal Execution**: Releases assets on valid signature
- **TSS Key Update**: Updates TSS public key during Churn

#### Vault Address

TSS-controlled address for asset custody:

- **Multi-chain**: Same TSS key generates addresses for all chains
- **No Private Key**: No single entity holds the private key
- **Threshold Control**: Requires 2/3 Maintainers to sign

### Off-Chain Components (Maintainer Node)

#### Observer

Monitors cross-chain events:

- **Event Detection**: Watches for deposits, withdrawals
- **Data Parsing**: Extracts cross-chain parameters
- **Submission**: Reports observations to MAP Relay Chain

#### Signer

Participates in TSS operations:

- **KeyGen**: Generates key share during TSS setup
- **KeySign**: Participates in transaction signing
- **Key Storage**: Securely stores local key share

#### P2P Network

Communication layer between Maintainers:

- **Discovery**: Finds other Maintainer nodes
- **Messaging**: Exchanges TSS protocol messages
- **Consensus**: Coordinates signing sessions

## Data Flow

### Inbound Transaction (TxIn)

```
1. User deposits to Vault on Source Chain
2. Observers detect deposit event
3. Observers submit observation to MAP Relay Chain
4. When 2/3+ observations match, TxIn is confirmed
5. Relay contract records the inbound transaction
6. User's balance is credited on target representation
```

### Outbound Transaction (TxOut)

```
1. User requests withdrawal on MAP Relay Chain
2. Relay contract creates TxOut record
3. Maintainers receive signing request
4. TSS KeySign produces signature
5. For contract chains: signature submitted to Gateway
6. For Bitcoin: signed transaction broadcast to network
7. Assets released from Vault to user
```

## Security Architecture

### Threshold Security

- **2/3 Threshold**: Requires 2/3 of Maintainers for any operation
- **No Single Point of Failure**: No individual can control assets
- **Byzantine Fault Tolerance**: System continues with up to 1/3 malicious nodes

### Slashing Conditions

| Violation | Penalty |
|-----------|---------|
| Double signing | Severe slash + jail |
| Offline during KeySign | Slash points accumulation |
| Invalid observation | Slash points |
| Failure to participate in KeyGen | Jail |

### Key Rotation (Churn)

Regular key rotation ensures:
- Removal of compromised Maintainers
- Addition of new Maintainers
- Fresh key material

## Integration with Light Client

Protocol v2 can optionally integrate light client verification:

```
┌─────────────────────────────────────────────────────────────────┐
│                     Enhanced Verification                        │
│                                                                  │
│  1. TSS signature provides fast finality                        │
│  2. Light client provides additional cryptographic proof        │
│  3. Either can be used independently or combined                │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

This hybrid approach offers:
- **Speed**: TSS signature for fast confirmation
- **Security**: Light client for trustless verification
- **Flexibility**: Choose based on use case requirements
