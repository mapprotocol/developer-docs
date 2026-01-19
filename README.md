# MAP Protocol Developer Documentation

Welcome to the MAP Protocol developer documentation. This documentation is designed for advanced developers who want to understand the technical details of MAP Protocol's cross-chain infrastructure.

## What is MAP Protocol?

MAP Protocol is a peer-to-peer cross-chain infrastructure that enables secure and decentralized interoperability between heterogeneous blockchains. It provides two complementary cross-chain solutions:

- **Protocol 2.0**: TSS (Threshold Signature Scheme) based decentralized custody
- **Protocol 1.0**: Light client-based trustless verification

## Documentation Structure

### [Overview](docs/overview/introduction.md)
Introduction to MAP Protocol, architecture overview, and comparison between v1 and v2.

### [Protocol 2.0](docs/protocol-v2/overview.md)
TSS-based cross-chain solution: architecture, maintainer roles, TSS mechanism, MStack, cross-chain flow, gas mechanism, security, and slashing.

### [Protocol 1.0](docs/protocol-v1/overview.md)
Light client-based cross-chain solution: light client design, MOS layer, and chain integration guides.

### [Relay Chain](docs/relay-chain/architecture.md)
MAP Relay Chain (Atlas) technical details: architecture, consensus mechanism, genesis contracts, and precompile contracts.

### Fundamentals
Core blockchain and smart contract concepts:
- [Blockchain](docs/fundamentals/blockchain/accounts.md): accounts, transactions, blocks, gas, MPT, RLP, oracle
- [Smart Contracts](docs/fundamentals/smart-contracts/evm.md): EVM, development, testing, security

### Appendix
- [BTC Layer2](docs/appendix/btc-layer2/overview.md): BTC Layer2 and BRC-201

## Quick Links

| Topic | Description |
|-------|-------------|
| [Architecture](docs/overview/architecture.md) | Overall system architecture |
| [v1 vs v2](docs/overview/v1-vs-v2.md) | Comparison of two solutions |
| [TSS](docs/protocol-v2/tss.md) | Threshold signature scheme |
| [Light Client](docs/protocol-v1/light-client/overview.md) | Light client design |
| [Consensus](docs/relay-chain/consensus/pos.md) | Proof of Stake consensus |
| [Genesis Contracts](docs/relay-chain/genesis-contracts/overview.md) | Genesis contract design |

## For Users and Operators

If you're looking for:
- Node operation guides
- API/SDK usage
- Running Compass-TSS

Please visit the [User Documentation](../docs/README.md) instead.

## Contributing

We welcome contributions to improve this documentation. Please submit issues or pull requests to our GitHub repository.

## Resources

- [GitHub](https://github.com/mapprotocol)
- [Website](https://mapprotocol.io)
- [Discord](https://discord.gg/mapprotocol)
