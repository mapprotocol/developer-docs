# MStack: Universal Cross-Chain Standard for Heterogeneous Blockchains

**Version**: 1.0-draft  
**Status**: Draft  
**Author**: MAP Protocol Team

---

## Table of Contents

1. [Overview](#1-overview)
2. [Design Principles](#2-design-principles)
3. [Message Format Specification](#3-message-format-specification)
4. [Operation Type Definitions](#4-operation-type-definitions)
5. [Chain Identifier Specification](#5-chain-identifier-specification)
6. [Token Identifier Specification](#6-token-identifier-specification)
7. [Address Format Specification](#7-address-format-specification)
8. [Amount Representation Specification](#8-amount-representation-specification)
9. [Extension Field Specification](#9-extension-field-specification)
10. [Security Considerations](#10-security-considerations)
11. [Appendix](#11-appendix)

---

## 1. Overview

### 1.1 Background

As the blockchain ecosystem evolves, cross-chain interoperability has become a critical requirement. However, significant technical differences exist across different blockchains:

- **Contract Chains** (e.g., Ethereum, Solana): Support smart contracts and can transmit structured data through events
- **Non-Contract Chains** (e.g., Bitcoin, Dogecoin): Do not support smart contracts and can only transmit limited information through transaction memo/OP_RETURN fields

These differences make it difficult to unify cross-chain protocols, increasing development complexity and user comprehension costs.

### 1.2 What is MStack

**MStack** (Memo-based Stack Protocol) is a universal cross-chain standard specification for heterogeneous chains based on memo fields. It defines a concise, compact, and extensible message format that enables:

- Non-contract chains to initiate and receive cross-chain requests via memo fields
- Contract chains to be compatible with the same semantics through equivalent event parameters
- Different cross-chain protocols to interoperate based on a unified standard

### 1.3 Design Goals

| Goal | Description |
|------|-------------|
| **Universality** | Applicable to all blockchains that support memo/note fields |
| **Compactness** | Minimize message size to accommodate strict limits like 80 bytes |
| **Readability** | Human-readable for easy debugging and verification |
| **Extensibility** | Support future addition of operation types and fields |
| **Security** | Prevent injection, replay, and other attacks |

### 1.4 Scope of Application

MStack is applicable to the following scenarios:

- Cross-chain asset transfers (Swap)
- Cross-chain message passing
- Cross-chain liquidity management
- Cross-chain asset migration
- Cross-chain refunds

---

## 2. Design Principles

### 2.1 Memo First

Considering the limitations of non-contract chains, MStack is designed with memo format as the core, with contract chains achieving compatibility through adaptation layers.

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           MStack Design Philosophy                           │
└─────────────────────────────────────────────────────────────────────────────┘

                    ┌─────────────────┐
                    │   MStack Memo   │
                    │ (Core Specification) │
                    └────────┬────────┘
                             │
           ┌─────────────────┼─────────────────┐
           │                 │                 │
           ▼                 ▼                 ▼
    ┌─────────────┐   ┌─────────────┐   ┌─────────────┐
    │   Bitcoin   │   │   Ethereum  │   │   Solana    │
    │  OP_RETURN  │   │   Event     │   │   Memo      │
    └─────────────┘   └─────────────┘   └─────────────┘
```

### 2.2 Prefix-Driven

Each operation type is identified by a unique prefix for quick identification and routing:

```
M + Operator + Separator + Parameters...

Where:
- M: MStack protocol identifier
- Operator: Single character identifying the operation type
- Separator: | character
- Parameters: Fields arranged in order
```

### 2.3 Field Compression

To accommodate memo size limits, the following compression strategies are employed:

| Strategy | Description |
|----------|-------------|
| Short Aliases | Chain and token names use 1-8 character aliases |
| Scientific Notation | Amounts support `12e8` format |
| Optional Fields | Non-essential fields can be omitted |
| Address Compression | Support for shortened address formats (if available) |

### 2.4 Version Compatibility

Version identification capability is reserved to ensure compatibility for future upgrades:

```
// Possible future version format
M2x|...  // Version 2 cross-chain Swap
```

---

## 3. Message Format Specification

### 3.1 Basic Syntax

```
<Prefix><Operator> | <Field1> | <Field2> | ... | <FieldN>
```

**Rules:**

| Rule | Description |
|------|-------------|
| Prefix | Fixed as `M`, indicating MStack protocol |
| Operator | Single character, immediately follows prefix, no separator |
| Separator | Use `|` character to separate fields |
| Spaces | No spaces allowed between fields |
| Case | Chain names start with uppercase, token names are all uppercase |
| Encoding | UTF-8 encoding |

### 3.2 Operator Definitions

| Operator | Memo Prefix | Operation Type | Description |
|----------|-------------|----------------|-------------|
| `x` | `Mx` | Swap | Cross-chain asset exchange |
| `>` | `M>` | Transfer In | Cross-chain transfer in marker |
| `<` | `M<` | Refund | Cross-chain refund |
| `+` | `M+` | Add Liquidity | Add liquidity |
| `-` | `M-` | Remove Liquidity | Remove liquidity |
| `~` | `M~` | Migrate | Asset migration |
| `!` | `M!` | Message | Cross-chain message (extension) |
| `?` | `M?` | Query | Cross-chain query (extension) |

### 3.3 Reserved Operators

The following operators are reserved for future use:

```
@ # $ % ^ & * = 
```

### 3.4 Special Character Restrictions

MStack protocol **does not support escape mechanisms**. The following special characters are not supported in field values:

- Vertical bar `|` - Used as main field separator
- Other characters that conflict with separators

If user addresses or data contain these characters, alternative encoding schemes must be used or input data must be ensured not to contain conflicting characters.

---

## 4. Operation Type Definitions

### 4.1 Cross-Chain Swap (Mx)

Users exchange assets from the source chain to a specified token on the target chain.

**Format:**
```
Mx | Chain | Token | Receiver | Amount | Affiliate
```

**Field Descriptions:**

| # | Field | Required | Length | Description |
|---|-------|----------|--------|-------------|
| 1 | Chain | Yes | 1-6 | Target chain alias |
| 2 | Token | Yes | 3-8 | Target token alias |
| 3 | Receiver | Yes | Variable | Target chain receiving address |
| 4 | Amount | No | Variable | Expected minimum amount to receive |
| 5 | Affiliate | No | Variable | Affiliate information |

**Examples:**
```
// Full format
Mx|Eth|USDT|0x742d35cc6634c0532925a3b844bc9e7595f5812b|1000000|fx:15

// Minimal format (no minimum amount and Affiliate)
Mx|Eth|USDT|0x742d35cc6634c0532925a3b844bc9e7595f5812b

// With minimum amount, no Affiliate
Mx|Eth|USDT|0x742d35cc6634c0532925a3b844bc9e7595f5812b|1000000
```

**Semantics:**
- Cross-chain transfer source chain assets to target chain `Chain`
- Receive `Token` type assets on the target chain
- Assets sent to `Receiver` address
- If `Amount` is specified, actual received amount should not be less than this value
- If `Affiliate` is specified, fees are distributed according to rules

### 4.2 Cross-Chain Transfer In (M>)

Marks transactions transferred in from other chains, used for target chain to identify cross-chain source.

**Format:**
```
M> | FromChain | OrderId
```

**Field Descriptions:**

| # | Field | Required | Description |
|---|-------|----------|-------------|
| 1 | FromChain | Yes | Source chain alias |
| 2 | OrderId | Yes | Source chain transaction hash or order ID |

**Example:**
```
M>|Eth|0x1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef
```

**Semantics:**
- Identifies that this transaction came from `FromChain` via cross-chain
- `OrderId` is used to associate with the original transaction on the source chain

### 4.3 Cross-Chain Refund (M<)

When cross-chain fails, assets are returned to the original sender.

**Format:**
```
M< | FromChain | OriginalOrderId
```

**Field Descriptions:**

| # | Field | Required | Description |
|---|-------|----------|-------------|
| 1 | FromChain | Yes | Original cross-chain source chain alias |
| 2 | OriginalOrderId | Yes | Original cross-chain transaction order ID |

**Example:**
```
M<|Eth|0x1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef
```

**Semantics:**
- Refund associated with `OriginalOrderId` failed cross-chain
- Assets returned to original sender address

### 4.4 Add Liquidity (M+)

LP Provider adds liquidity to the cross-chain protocol.

**Format:**
```
M+ | Receiver | Pool
```

**Field Descriptions:**

| # | Field | Required | Description |
|---|-------|----------|-------------|
| 1 | Receiver | Yes | LP token receiving address |
| 2 | Pool | No | Specified liquidity pool (defaults to general pool) |

**Examples:**
```
// Basic format
M+|0x742d35cc6634c0532925a3b844bc9e7595f5812b

// Specify pool
M+|0x742d35cc6634c0532925a3b844bc9e7595f5812b|btc-eth
```

**Semantics:**
- Add transferred assets as liquidity
- LP tokens sent to `Receiver` address

### 4.5 Remove Liquidity (M-)

LP Provider removes liquidity from the cross-chain protocol.

**Format:**
```
M- | Chain | Token | Receiver | Amount
```

**Field Descriptions:**

| # | Field | Required | Description |
|---|-------|----------|-------------|
| 1 | Chain | Yes | Target chain for withdrawn assets |
| 2 | Token | Yes | Token type to withdraw |
| 3 | Receiver | Yes | Receiving address |
| 4 | Amount | No | LP amount to withdraw (defaults to all) |

**Example:**
```
M-|Btc|NATIVE|bc1qxy2kgdygjrsqtzq2n0yrf2493p83kkfjhx0wlh|500000
```

**Semantics:**
- Burn specified amount of LP tokens
- Withdraw `Token` assets on `Chain` to `Receiver`

### 4.6 Asset Migration (M~)

During TSS switching, migrate assets from old Vault to new Vault.

**Format:**
```
M~ | Epoch | NewVault | BatchId
```

**Field Descriptions:**

| # | Field | Required | Description |
|---|-------|----------|-------------|
| 1 | Epoch | Yes | Epoch when migration occurs |
| 2 | NewVault | Yes | New Vault address |
| 3 | BatchId | No | Batch ID (used for batch migrations) |

**Examples:**
```
// Single migration
M~|125|bc1qxy2kgdygjrsqtzq2n0yrf2493p83kkfjhx0wlh

// Batch migration
M~|125|bc1qxy2kgdygjrsqtzq2n0yrf2493p83kkfjhx0wlh|3
```

**Semantics:**
- Initiated by old Vault managers
- Transfer assets to `NewVault` address
- Used for asset migration during TSS key rotation

### 4.7 Cross-Chain Message (M!) - Extension

Used for cross-chain message passing without asset transfer.

**Format:**
```
M! | Chain | Contract | Payload
```

**Field Descriptions:**

| # | Field | Required | Description |
|---|-------|----------|-------------|
| 1 | Chain | Yes | Target chain alias |
| 2 | Contract | Yes | Target contract address |
| 3 | Payload | Yes | Message payload (hex encoded) |

**Example:**
```
M!|Eth|0x742d35cc6634c0532925a3b844bc9e7595f5812b|0x1234abcd
```

**Semantics:**
- Call specified contract on target chain
- Pass `Payload` as call data

---

## 5. Chain Identifier Specification

### 5.1 Alias Rules

| Rule | Description |
|------|-------------|
| Length | 1-6 characters |
| Case | First letter uppercase, rest lowercase |
| Character Set | Letters A-Z, a-z only |
| Uniqueness | Globally unique, no duplicates |

### 5.2 Predefined Chain Aliases

#### 5.2.1 Contract Chains

| Chain Name | Chain ID | Alias | Address Format |
|------------|----------|-------|----------------|
| Ethereum | 1 | `Eth` | 0x + 40 hex |
| BSC | 56 | `Bsc` | 0x + 40 hex |
| Polygon | 137 | `Pol` | 0x + 40 hex |
| Arbitrum | 42161 | `Arb` | 0x + 40 hex |
| Optimism | 10 | `Op` | 0x + 40 hex |
| Base | 8453 | `Base` | 0x + 40 hex |
| Linea | 59144 | `Linea` | 0x + 40 hex |
| Scroll | 534352 | `Scroll` | 0x + 40 hex |
| zkSync Era | 324 | `Zks` | 0x + 40 hex |
| Avalanche C-Chain | 43114 | `Avax` | 0x + 40 hex |
| Fantom | 250 | `Ftm` | 0x + 40 hex |
| Solana | 1360108768460801 | `Sol` | Base58, 32-44 chars |
| Tron | 728126428 | `Tron` | Base58, starts with T |
| MAP Protocol | 22776 | `Mapo` | 0x + 40 hex |

#### 5.2.2 Non-Contract Chains

| Chain Name | Alias | Address Format |
|------------|-------|----------------|
| Bitcoin | `Btc` | Bech32 (bc1) / Base58 |
| Dogecoin | `Doge` | Base58, starts with D |
| Litecoin | `Ltc` | Bech32 (ltc1) / Base58 |
| Bitcoin Cash | `Bch` | CashAddr / Base58 |
| XRP Ledger | `Xrp` | Base58, starts with r |

### 5.3 Alias Registration

New chain aliases must be registered through governance process:

```solidity
struct ChainRegistry {
    uint256 chainId;       // Chain ID
    string name;           // Chain name
    string alias;          // MStack alias
    ChainType chainType;   // CONTRACT / NON_CONTRACT
    string addressRegex;   // Address validation regex
    bool enabled;          // Whether enabled
}
```

---

## 6. Token Identifier Specification

### 6.1 Alias Rules

| Rule | Description |
|------|-------------|
| Length | 3-8 characters |
| Case | All uppercase |
| Character Set | Letters A-Z and digits 0-9 only |
| Uniqueness | Unique within the same chain |

### 6.2 Predefined Token Aliases

#### 6.2.1 Universal Aliases

| Token Type | Alias | Description |
|------------|-------|-------------|
| Native Token | `NATIVE` | Chain's native asset (ETH, BTC, SOL, etc.) |
| Wrapped Native Token | `WRAP` | Wrapped version (WETH, WBTC, etc.) |

#### 6.2.2 Stablecoins

| Token | Alias | Description |
|-------|-------|-------------|
| USDT | `USDT` | Tether USD |
| USDC | `USDC` | USD Coin |
| DAI | `DAI` | MakerDAO DAI |
| BUSD | `BUSD` | Binance USD |
| TUSD | `TUSD` | TrueUSD |

#### 6.2.3 Major Assets

| Token | Alias | Description |
|-------|-------|-------------|
| ETH | `ETH` | Ethereum (version on non-ETH chains) |
| BTC | `BTC` | Bitcoin (version on non-BTC chains) |
| BNB | `BNB` | Binance Coin |
| MATIC | `MATIC` | Polygon |
| SOL | `SOL` | Solana |

### 6.3 Token Registration

```solidity
struct TokenRegistry {
    uint256 chainId;       // Chain ID
    address tokenAddress;  // Token contract address
    string symbol;         // Original symbol
    string alias;          // MStack alias
    uint8 decimals;        // Decimal places
    bool enabled;          // Whether enabled
}
```

### 6.4 Cross-Chain Token Mapping

The same asset maintains consistent aliases across different chains:

```
USDT has the alias USDT on all chains:
├─▶ Ethereum: 0xdAC17F958D2ee523a2206206994597C13D831ec7 → USDT
├─▶ BSC: 0x55d398326f99059fF775485246999027B3197955 → USDT
├─▶ Polygon: 0xc2132D05D31c914a87C6611C10748AEb04B58e8F → USDT
└─▶ Tron: TR7NHqjeKQxGTCi8q8ZY4pL8otSzgjLj6t → USDT
```

---

## 7. Address Format Specification

### 7.1 Format Requirements

Different chains have different address formats. MStack specifies the following unified handling rules:

| Chain Type | Format Requirement | Validation Rule |
|------------|-------------------|-----------------|
| EVM Chains | Lowercase, 0x prefix, 40 hex characters | `^0x[a-f0-9]{40}$` |
| Tron | Base58, 34 characters, starts with T | `^T[A-HJ-NP-Za-km-z1-9]{33}$` |
| Solana | Base58, 32-44 characters | `^[1-9A-HJ-NP-Za-km-z]{32,44}$` |
| Bitcoin (Bech32) | Starts with bc1 | `^bc1[a-z0-9]{39,59}$` |
| Bitcoin (Legacy) | Starts with 1 or 3 | `^[13][a-km-zA-HJ-NP-Z1-9]{25,34}$` |
| Dogecoin | Starts with D, Base58 | `^D[5-9A-HJ-NP-U][1-9A-HJ-NP-Za-km-z]{32}$` |

### 7.2 Case Rules

| Chain | Rule | Description |
|-------|------|-------------|
| EVM | Lowercase | Uniformly use lowercase, ignore checksum |
| Tron | Case-sensitive | Must preserve original case |
| Solana | Case-sensitive | Must preserve original case |
| Bitcoin | Lowercase (Bech32) | Bech32 addresses use lowercase |

### 7.3 Address Compression (Optional)

When memo space is tight, address compression can be used:

```
// Full address
0x742d35cc6634c0532925a3b844bc9e7595f5812b

// Compressed format (requires registry support)
@user123

// Or use prefix/suffix
0x742d...812b
```

**Note:** Address compression requires off-chain registry support and is not recommended for core specification use.

---

## 8. Amount Representation Specification

### 8.1 Basic Format

Amounts are represented as integers in the token's smallest unit.

```
// USDT (6 decimal places)
1000000 = 1 USDT
1500000 = 1.5 USDT

// ETH (18 decimal places)
1000000000000000000 = 1 ETH
```

### 8.2 Scientific Notation

To save space, scientific notation is supported:

```
Format: <mantissa>e<exponent>

Examples:
1e6     = 1,000,000
15e5    = 1,500,000
1e18    = 1,000,000,000,000,000,000
123e4   = 1,230,000
```

### 8.3 Simplified Representation

For common amounts, simplified representation is supported:

```
// Using k, m, b suffixes (optional extension)
1k = 1,000
1m = 1,000,000
1b = 1,000,000,000

Examples:
1.5m = 1,500,000
```

### 8.4 Precision Handling

```
Precision handling during cross-chain:

1. Source chain amount uses source chain token's precision
2. Target chain amount uses target chain token's precision
3. If precisions differ, protocol layer handles conversion
4. Amount field represents minimum receiving amount in target chain precision
```

---

## 9. Extension Field Specification

### 9.1 Affiliate Field

Used to identify affiliates and fee sharing.

**Format:**
```
<partner_id>:<fee_bps>/<partner_id>:<fee_bps>/...
```

**Rules:**

| Rule | Description |
|------|-------------|
| partner_id | 1-4 character affiliate identifier |
| fee_bps | Fee in basis points (1 bps = 0.01%) |
| Separator | Multiple affiliates separated by `/` |

**Examples:**
```
// Single affiliate, charging 0.15%
fx:15

// Single affiliate, identifier only, no fee
tp

// Multiple affiliates
okx:10/b:10    // OKX and Butter each charge 0.1%
```

### 9.2 Affiliate Registration

```solidity
struct AffiliateRegistry {
    string partnerId;      // Affiliate ID
    string name;           // Affiliate name
    address feeReceiver;   // Fee receiving address
    uint256 maxFeeBps;     // Maximum fee rate
    bool enabled;          // Whether enabled
}
```

### 9.3 Custom Extensions

MStack supports passing custom data through `extra` field:

```
// Format
Mx|Chain|Token|Receiver|Amount|Affiliate|Extra

// Extra uses key:value format
Mx|Eth|USDT|0x...|1000000|fx:15|slippage:50,deadline:1234567890
```

---

## 10. Security Considerations

### 10.1 Injection Attack Prevention

**Risk:** Maliciously constructed memos may cause parsing errors or injection attacks.

**Protection Measures:**

```
1. Strict field format validation
   ├─▶ Chain alias: Only allow registered values
   ├─▶ Token alias: Only allow registered values
   └─▶ Address: Must conform to target chain format

2. Special character handling
   ├─▶ No escape support, reject field values containing separators
   └─▶ Reject memos containing control characters

3. Length limits
   └─▶ Maximum length limits for each field
```

### 10.2 Replay Attack Prevention

**Risk:** The same memo may be reused.

**Protection Measures:**

```
1. Order ID mechanism
   └─▶ Generate unique ID for each transaction: hash(chain + txHash + logIndex)

2. Execution status recording
   └─▶ Executed Order IDs are recorded on-chain

3. Timeliness check
   └─▶ Optional deadline field
```

### 10.3 Field Length Limits

| Field | Maximum Length |
|-------|---------------|
| Chain | 6 characters |
| Token | 8 characters |
| Receiver | Per target chain specification |
| Amount | Variable |
| Affiliate | Variable |
| Total Memo | Per chain limits (see Appendix 11.3) |

**Note:** MStack specification itself does not set a unified memo length limit. Each chain implementation handles this according to actual limits. For constrained chains like Bitcoin (80 bytes), compression techniques such as scientific notation for amounts and omitting optional fields are recommended.

---

## 11. Appendix

### 11.1 Complete Examples

#### 11.1.1 Bitcoin → Ethereum USDT

```
User Operation:
- Send 0.1 BTC to Vault address from Bitcoin
- Expect to receive USDT on Ethereum

Memo:
Mx|Eth|USDT|0x742d35cc6634c0532925a3b844bc9e7595f5812b|3000e6|fx:15

Parsed:
- Operation: Swap (Mx)
- Target Chain: Ethereum (Eth)
- Target Token: USDT
- Receiving Address: 0x742d35cc6634c0532925a3b844bc9e7595f5812b
- Minimum Amount: 3000 USDT (3000e6 = 3,000,000,000 / 1e6)
- Affiliate: FlashX charges 0.15%
```

#### 11.1.2 Ethereum → Bitcoin

```
User Operation:
- Send USDT to Gateway contract from Ethereum
- Expect to receive BTC on Bitcoin

Memo Equivalent Format:
Mx|Btc|NATIVE|bc1qxy2kgdygjrsqtzq2n0yrf2493p83kkfjhx0wlh

Parsed:
- Operation: Swap (Mx)
- Target Chain: Bitcoin (Btc)
- Target Token: NATIVE (native BTC)
- Receiving Address: bc1qxy2kgdygjrsqtzq2n0yrf2493p83kkfjhx0wlh
```

#### 11.1.3 Add BTC Liquidity

```
User Operation:
- Send BTC to Vault address
- Receive LP tokens to specified address

Memo:
M+|0x742d35cc6634c0532925a3b844bc9e7595f5812b

Parsed:
- Operation: Add Liquidity (M+)
- LP Receiving Address: 0x742d35cc6634c0532925a3b844bc9e7595f5812b
```

### 11.2 Error Code Definitions

| Error Code | Description |
|------------|-------------|
| `E001` | Invalid memo format |
| `E002` | Unknown operation type |
| `E003` | Missing required fields |
| `E004` | Unknown chain alias |
| `E005` | Unknown token alias |
| `E006` | Invalid address format |
| `E007` | Invalid amount format |
| `E008` | Invalid Affiliate format |
| `E009` | Memo length exceeded |
| `E010` | Field length exceeded |

### 11.3 Memo Size Limit Reference

| Chain | Limit | Description |
|-------|-------|-------------|
| Bitcoin | 80 bytes | OP_RETURN standard limit |
| Dogecoin | 80 bytes | Similar to Bitcoin |
| Litecoin | 80 bytes | OP_RETURN limit |
| XRP Ledger | 256 bytes | Memo field limit |
| Cosmos-based | 256 bytes | Memo field limit |
| Solana | 566 bytes | Memo instruction limit |
| EVM Chains | No practical limit | Passed via calldata |

**Note:** MStack specification itself does not set a unified limit. Each chain implementation handles according to above limits. For 80-byte constrained chains, compression strategies such as scientific notation for amounts and omitting optional fields are recommended.

### 11.4 BNF Grammar Definition

```bnf
<mstack-memo>    ::= <prefix> <operator> <fields>?
<prefix>         ::= "M"
<operator>       ::= "x" | ">" | "<" | "+" | "-" | "~" | "!" | "?"
<fields>         ::= "|" <field> (<separator> <field>)*
<separator>      ::= "|"
<field>          ::= <chain> | <token> | <address> | <amount> | <affiliate> | <custom>
<chain>          ::= [A-Z][a-z]{0,5}
<token>          ::= [A-Z0-9]{3,8}
<address>        ::= <evm-address> | <btc-address> | <sol-address> | <tron-address>
<evm-address>    ::= "0x" [a-f0-9]{40}
<btc-address>    ::= "bc1" [a-z0-9]{39,59} | [13][a-km-zA-HJ-NP-Z1-9]{25,34}
<sol-address>    ::= [1-9A-HJ-NP-Za-km-z]{32,44}
<tron-address>   ::= "T" [A-HJ-NP-Za-km-z1-9]{33}
<amount>         ::= [0-9]+ ("e" [0-9]+)?
<affiliate>      ::= <partner-id> (":" [0-9]+)? ("/" <affiliate>)*
<partner-id>     ::= [a-z]{1,4}
<custom>         ::= <key> ":" <value> ("," <custom>)*
<key>            ::= [a-z]+
<value>          ::= [^,|]+
```

### 11.5 Version History

| Version | Date | Description |
|---------|------|-------------|
| 1.0-draft | 2024-XX | Initial draft |

---

## Contributing

MStack is an open standard and community contributions are welcome.
