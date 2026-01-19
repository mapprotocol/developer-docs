# Gas Mechanism

## Overview

MAP Protocol v2 accurately estimates gas fees for each chain to ensure cross-chain transactions execute successfully without overcharging users. This document covers gas calculation, caching, oracles, and fee update mechanisms.

## EVM Chain Gas Calculation

### Gas Data Collection

Maintainer nodes obtain gas data by monitoring blocks, extracting the base fee and priority fees from block transactions.

### Priority Fee Calculation

The system calculates a reasonable priority fee using the 25th percentile of priority fees from block transactions. This approach avoids outliers while ensuring transactions get included. The final gas price combines the base fee with the selected priority fee, with a minimum threshold applied.

### Gas Cache Mechanism

To smooth gas price fluctuations, the system maintains a rolling cache of recent gas prices. The cache size is configurable via the `gas_cache_blocks` parameter, keeping only the most recent entries.

### Transaction Gas Calculation

When sending transactions, the system uses statistical analysis:

1. Calculate the mean of cached gas prices
2. Calculate the standard deviation
3. Set gas fee cap = mean + 3 × standard deviation
4. Calculate tip cap as a percentage of fee cap

| Parameter | Description |
|-----------|-------------|
| gas_price_resolution | Minimum gas price per chain |
| gas_cache_blocks | Number of blocks for gas cache |
| max_gas_tip_percentage | Maximum tip percentage |

## Bitcoin Chain Fee Calculation

### Fee Data Retrieval

The system retrieves network fee information including minimum relay fee and block average fee rate from Bitcoin RPC calls.

### Fee Rate Calculation

The fee rate is calculated to ensure the estimated transaction fee meets the network minimum relay fee requirement. If the initial estimate falls short, the rate is adjusted upward. A minimum sats-per-vbyte threshold is also enforced.

### Transaction Size Estimation

Bitcoin transaction sizes are estimated based on:

| Component | Size |
|-----------|------|
| Base overhead | ~10 bytes |
| Per input (P2WPKH) | ~68 bytes |
| Per output (P2WPKH) | ~31 bytes |

The system estimates sizes before UTXO selection to predict fees accurately.

## Gas Oracle

### Oracle Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     Gas Oracle System                        │
└─────────────────────────────────────────────────────────────┘

  Maintainer Nodes              MAP Relay Chain
       │                              │
       ▼                              ▼
┌─────────────┐               ┌─────────────┐
│  Observe    │──────────────▶│   GasOracle │
│  Gas Prices │               │   Contract  │
└─────────────┘               └─────────────┘
       │                              │
       │                              ▼
       │                      ┌─────────────┐
       │                      │  Aggregate  │
       │                      │   & Store   │
       │                      └─────────────┘
       │                              │
       ▼                              ▼
┌─────────────┐               ┌─────────────┐
│  Use for    │◀──────────────│   Provide   │
│  Estimation │               │   Gas Data  │
└─────────────┘               └─────────────┘
```

### Reporting Process

1. Each Maintainer observes gas prices on external chains
2. Maintainers periodically report observations to GasOracle contract
3. Contract aggregates reports, using median value for consensus
4. Aggregated gas prices stored on-chain for cross-chain fee calculation

### Aggregation Method

The oracle uses median aggregation to resist manipulation:
- Collects reports from multiple Maintainers
- Sorts reported values
- Takes median as the consensus price
- Updates only when sufficient reports received

## Cross-chain Fee Calculation

### Fee Components

| Component | Description |
|-----------|-------------|
| Target chain gas | Estimated execution cost on target chain |
| Protocol fee | Fee for protocol operation and LP rewards |
| Buffer | Safety margin for gas price fluctuations |

### Calculation Flow

```
User Request ──▶ Estimate Target Gas ──▶ Add Protocol Fee ──▶ Apply Buffer ──▶ Final Fee
      │                  │                      │                  │              │
      ▼                  ▼                      ▼                  ▼              ▼
  Source chain     GasOracle data         Configurable        Percentage      Deducted from
  amount           + operation type       per chain           multiplier      transfer amount
```

### Fee Deduction

Cross-chain fees are deducted from the transfer amount:
- User sends X tokens on source chain
- Fee F is calculated based on target chain costs
- User receives (X - F) tokens on target chain

## Gas Price Updates

### Update Triggers

| Trigger | Description |
|---------|-------------|
| Block interval | Regular updates every N blocks |
| Price deviation | Update when price changes > threshold |
| Manual override | Emergency updates by governance |

### Staleness Protection

The system includes staleness checks:
- Gas prices have a maximum age
- Stale prices trigger warnings or fallback to defaults
- Critical operations may be paused if gas data too old

## Chain-specific Considerations

### EVM Chains

- Support EIP-1559 fee model (base fee + priority fee)
- Dynamic base fee adjustment based on network congestion
- Priority fee for transaction ordering preference

### Bitcoin-like Chains

- Fee rate in satoshis per virtual byte
- UTXO-based transaction size estimation
- Replace-By-Fee (RBF) support for stuck transactions

### Other Chains

Each chain type may have unique gas/fee models:

| Chain Type | Fee Model |
|------------|-----------|
| Solana | Compute units + priority fee |
| NEAR | Gas units with conversion rate |
| TON | Gas units with forwarding fees |

## Configuration Parameters

| Parameter | Description | Default |
|-----------|-------------|---------|
| gas_cache_blocks | Blocks to cache for averaging | 20 |
| gas_price_resolution | Minimum gas price unit | Chain-specific |
| max_gas_tip_percentage | Max tip as % of base | 25% |
| gas_update_interval | Blocks between updates | 10 |
| gas_staleness_threshold | Max age before stale | 100 blocks |
