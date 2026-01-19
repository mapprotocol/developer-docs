# Maintainer Management

## Overview

Maintainers are core participants in the MAP Protocol v2.0 cross-chain system, responsible for monitoring cross-chain events, participating in TSS signing, and submitting observation results. This chapter details the Maintainer lifecycle management, including registration, updates, election, state transitions, and TSS switching processes.

## Maintainer State Machine

Maintainers go through the following states during their lifecycle:

```
                                   Register
                                     │
                                     ▼
┌──────────┐                   ┌─────────────┐
│ UNKNOWN  │                   │ WHITELISTED │
│          │                   │             │
└──────────┘                   └─────────────┘
                                     │
                                     │ Verification Passed
                                     ▼
                               ┌─────────────┐
                               │   STANDBY   │◀─────────────┐
                               │             │              │
                               └─────────────┘              │
                                     │                      │
                                     │ Elected              │ Exit Active Set
                                     ▼                      │
                               ┌─────────────┐              │
                               │    READY    │              │
                               │             │              │
                               └─────────────┘              │
                                     │                      │
                                     │ KeyGen Success       │
                                     ▼                      │
                               ┌─────────────┐              │
                               │   ACTIVE    │──────────────┘
                               │             │
                               └─────────────┘
                                     │
                                     │ Voluntary Withdrawal/Slashed
                                     ▼
                               ┌─────────────┐
                               │  DISABLED   │
                               │             │
                               └─────────────┘
```

| State | Description |
|-------|-------------|
| UNKNOWN | Initial state, unregistered address |
| WHITELISTED | Registered, waiting for verification to join whitelist |
| STANDBY | Standby state, can participate in election but not selected |
| READY | Elected, waiting for KeyGen to complete |
| ACTIVE | Active state, participating in cross-chain signing |
| DISABLED | Disabled, no longer participates in any operations |

## Registration and Updates

### Registration

Validators register as Maintainer candidates by calling the `register` function.

**Function Signature:**
```solidity
function register(
    bytes calldata secp256Pubkey,
    bytes calldata ed25519PubKey,
    string calldata p2pAddress
) external
```

**Parameter Description:**

| Parameter | Type | Description |
|-----------|------|-------------|
| secp256Pubkey | bytes | secp256k1 public key for TSS signing |
| ed25519PubKey | bytes | ed25519 public key for P2P communication authentication |
| p2pAddress | string | P2P network address for inter-node communication |

**Prerequisites:**
- Caller must be a Validator of MAP Relay Chain
- Caller has not yet registered as a Maintainer

**Registration Flow:**
```
1. Validator calls register()
   └─▶ Verify caller is a valid Validator
       └─▶ Verify public key format is correct
           └─▶ Store Maintainer information
               └─▶ Set state to WHITELISTED
                   └─▶ Emit MaintainerRegistered event
```

### Update

Registered Maintainers can update their public keys and P2P address information.

**Function Signature:**
```solidity
function update(
    bytes calldata secp256Pubkey,
    bytes calldata ed25519PubKey,
    string calldata p2pAddress
) external
```

**Restrictions:**
- Only Maintainers in STANDBY or WHITELISTED state can update information
- ACTIVE Maintainers cannot update, must wait until they exit the active set

### Deregistration

Maintainers can voluntarily deregister.

**Function Signature:**
```solidity
function deRegister() external
```

**Deregistration Flow:**
```
1. Maintainer calls deRegister()
   └─▶ Check current state
       ├─▶ [ACTIVE] Mark as pending exit, wait for current epoch to end
       └─▶ [Other states] Directly set to DISABLED
           └─▶ Emit MaintainerDeRegistered event
```

**Notes:**
- ACTIVE Maintainers must wait for TSS switching to complete before fully exiting after deregistration
- Deregistration does not immediately return stake, must wait for unlock period

## Election Mechanism

Elections are automatically triggered at the end of each epoch, selecting a new active set from STANDBY Maintainers.

### Election Trigger

**Function Signature:**
```solidity
function election() external onlyVm
```

**Trigger Conditions:**
- Called by MAP Relay Chain system contract during epoch transitions
- Only callable at the VM level

### Election Rules

```
Election Flow:

1. Get current epoch's Validator set
   │
2. Filter conditions:
   ├─▶ Must be a registered Maintainer (STANDBY state)
   ├─▶ JailEpoch must be 0 (not jailed)
   └─▶ SlashPoint below BadMaintainerSlashPointThreshold
   │
3. Sorting rules:
   ├─▶ Priority by stake amount (high to low)
   └─▶ Nodes with SlashPoint exceeding threshold are prioritized for exclusion
   │
4. Select Maintainers:
   ├─▶ Select top N nodes after sorting (N is configured Maintainer count)
   └─▶ Ensure changes between old and new sets don't exceed 1/3
   │
5. Generate new Vault:
   └─▶ If Maintainer set changes, trigger KeyGen
```

### 1/3 Change Limit

To ensure system stability and security, during each TSS switch:

- **No more than 1/3** of members can be removed from the original Maintainer set
- This ensures the system can operate normally even if some nodes have issues
- If more than 1/3 of nodes need to be removed, it will be done gradually over multiple epochs

### Election Results

After election completion:
- Selected Maintainers change state to READY
- Unselected ones remain in STANDBY state
- KeyGen event is triggered, notifying nodes to begin key generation

## TSS Switching (Churn) Process

TSS switching refers to the process of generating a new Vault and migrating assets when the Maintainer set changes.

### Overall Flow

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           TSS Switching Flow                                 │
└─────────────────────────────────────────────────────────────────────────────┘

  Epoch End                 KeyGen                    Vote Confirmation
      │                       │                            │
      ▼                       ▼                            ▼
┌──────────┐            ┌──────────┐            ┌──────────┐
│ Election │───────────▶│  KeyGen  │───────────▶│  Vote    │
│          │            │ Generate │            │  Update  │
│          │            │   Key    │            │ TssPool  │
└──────────┘            └──────────┘            └──────────┘
                                                      │
      ┌───────────────────────────────────────────────┘
      │
      ▼
┌──────────┐            ┌──────────┐            ┌──────────┐
│  State   │───────────▶│  Asset   │───────────▶│ Complete │
│  Update  │            │ Migration│            │ Confirm  │
└──────────┘            └──────────┘            └──────────┘
      │                       │                        │
      ▼                       ▼                        ▼
  Vault State            Per-chain Asset         Retiring→Retired
  Active→Retiring        Transfer to             Final State
                         New Vault               Confirmation
```

### KeyGen Flow

After election produces a new Maintainer set, TSS key generation is required.

**KeyGen Parameter Structure:**
```solidity
struct TssPoolParam {
    bytes32 id;           // Unique identifier
    uint256 epoch;        // Belonging epoch
    bytes pubkey;         // Generated TSS public key
    address[] members;    // Maintainer member list
    uint256[] chainIds;   // Supported chain ID list
    address[] blames;     // Responsible nodes on failure
    bytes signature;      // Aggregated signature
}
```

**KeyGen Flow:**
```
1. Election complete, emit KeyGen event
   └─▶ Event contains: epoch, participating nodes list, supported chains list

2. Maintainer nodes receive event
   └─▶ Coordinate KeyGen through P2P network
       └─▶ Each node generates private key share
           └─▶ Aggregate to generate public key

3. KeyGen success
   └─▶ Each node submits voteUpdateTssPool
       └─▶ Reach 2/3+ consensus
           └─▶ New Vault created, state is Active

4. KeyGen failure
   └─▶ Submit failure report with blame nodes
       └─▶ Penalize blame nodes
           └─▶ Wait for next epoch re-election
```

### Vault State Transition

After KeyGen success, Vault states transition:

| Phase | Old Vault | New Vault |
|-------|-----------|-----------|
| Before KeyGen success | Active | - |
| After KeyGen success | Retiring | Active |
| After asset migration complete | Retired | Active |

### Exception Handling

| Exception | Handling |
|-----------|----------|
| KeyGen failure | Trigger next round election at current epoch end, doesn't affect existing Vault operation |
| Vote doesn't reach consensus | Wait for epoch end to re-elect, existing Vault continues service |
| Partial consensus reached | Next churn continues to attempt completion |
| Migration tx fails on-chain | Keep retrying, don't proceed to next election until current migration completes |
| Node doesn't submit vote | Node gets SlashPoint recorded, re-election after epoch ends |

### Maintainer State Changes

After TSS switching completes:

| Node Type | State Change |
|-----------|--------------|
| Newly joined Maintainer | READY → ACTIVE |
| Continuing Maintainer | ACTIVE (maintained) |
| Exiting Maintainer | ACTIVE → STANDBY |

## Vault Management

### Vault Information Structure

```solidity
struct VaultInfo {
    bytes pubkey;              // TSS public key
    VaultState status;         // State: Active/Retiring/Retired
    uint256 epoch;             // Epoch when created
    address[] members;         // Maintainer members
    uint256[] chainIds;        // Supported chains
    VaultRouter[] routers;     // Gateway addresses per chain
    Token[] coins;             // Asset balances per chain
    uint64 inboundTxCount;     // Inbound transaction count
    uint64 outboundTxCount;    // Outbound transaction count
}
```

### Vault Sync Strategy

Maintainer nodes need to sync the following Vault information:

| Vault State | Sync Strategy |
|-------------|---------------|
| Active | Full sync, for processing new cross-chain requests |
| Retiring | Full sync, for completing asset migration |
| Retired | Sync those within recent N blocks (for handling delayed transactions and refunds) |

### Vault and Member Relationships

- Each Vault has a fixed Maintainer member list
- Different Vaults' members **can overlap** (same node may belong to both Active and Retiring Vaults)
- Nodes decide whether to participate in signing for a Vault based on whether they are members

## P2P Node Discovery

Maintainer nodes communicate through P2P network, node discovery methods:

### From On-chain

```solidity
function getMaintainers() external view returns (MaintainerInfo[] memory)
```

Returns all registered Maintainer information, including P2P addresses.

### MaintainerInfo Structure

```solidity
struct MaintainerInfo {
    MaintainerStatus status;   // Current state
    uint248 version;           // Version number
    address addr;              // On-chain address
    bytes secp256Pubkey;       // secp256k1 public key
    bytes ed25519Pubkey;       // ed25519 public key
    string p2pAddress;         // P2P network address
}
```
