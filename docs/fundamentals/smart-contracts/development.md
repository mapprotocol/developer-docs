---
title: Smart Contract Development
description: Frameworks, development networks, compiling, and deploying smart contracts.
lang: en
---

This guide covers the development workflow for smart contracts on MAPO-Relay-Chain, including frameworks, local development networks, compilation, and deployment.

## Development Frameworks

Building a complete decentralized application requires different technologies. Frameworks provide the features you need or a plugin system to select tools.

### Framework Features

- Local blockchain for development
- Smart contract editing and testing utilities
- Client-side application development integration
- Network connection and contract deployment configuration
- IPFS integration for decentralized storage

### Popular Frameworks

| Framework | Description | Links |
|-----------|-------------|-------|
| **Hardhat** | Flexible development environment with plugins | [hardhat.org](https://hardhat.org), [GitHub](https://github.com/nomiclabs/hardhat) |
| **Truffle** | Development environment with testing framework | [trufflesuite.com](https://www.trufflesuite.com/), [GitHub](https://github.com/trufflesuite/truffle) |
| **Foundry** | Fast Rust-based toolkit | [getfoundry.sh](https://getfoundry.sh/) |
| **Remix** | Browser-based IDE | [remix.ethereum.org](https://remix.ethereum.org) |
| **OpenZeppelin SDK** | Secure contract development | [openzeppelin.com/sdk](https://openzeppelin.com/sdk/) |
| **Tenderly** | Debugging and monitoring platform | [tenderly.co](https://tenderly.co/) |

## Development Networks

When developing smart contracts, you may want to test locally before deploying to a public network. Development networks offer faster iteration than public testnets.

### Public Testnets

MAPO maintains public testnets for testing before mainnet deployment.

### Local Development Tools

**Ganache** - Part of the Truffle suite, provides a personal blockchain for development:
- Desktop application (Ganache UI) and CLI (`ganache-cli`)
- [trufflesuite.com/ganache](https://www.trufflesuite.com/ganache)

**Hardhat Network** - Built-in local network for Hardhat:
- Deploy contracts, run tests, debug code
- [hardhat.org](https://hardhat.org/)

## Compiling Contracts

The EVM runs bytecode, so Solidity contracts must be compiled before deployment.

### Compilation Process

Solidity source code:
```solidity
pragma solidity 0.4.24;

contract Greeter {
    function greet() public constant returns (string) {
        return "Hello";
    }
}
```

Compiles to EVM bytecode:
```
PUSH1 0x80 PUSH1 0x40 MSTORE PUSH1 0x4 CALLDATASIZE LT PUSH2 0x41 JUMPI...
```

### Application Binary Interface (ABI)

The compiler also produces the ABI - a JSON file describing the contract's functions. This allows web applications to interact with the contract.

```json
[
  {
    "constant": true,
    "inputs": [],
    "name": "name",
    "outputs": [{"name": "", "type": "string"}],
    "type": "function"
  },
  {
    "constant": false,
    "inputs": [
      {"name": "_to", "type": "address"},
      {"name": "_value", "type": "uint256"}
    ],
    "name": "transfer",
    "outputs": [{"name": "", "type": "bool"}],
    "type": "function"
  }
]
```

JavaScript client libraries read the ABI to call contract functions from web applications.

## Deploying Contracts

To deploy a smart contract, send a transaction containing the compiled bytecode without specifying a recipient.

### Requirements

- **Contract bytecode** - Generated through compilation
- **MAPO coins for gas** - Deployment requires more gas than simple transfers
- **Deployment script or plugin** - Automates the deployment process
- **Access to a MAPO node** - Run your own or connect to a public node

### Deployment Steps

1. Compile your contract to get bytecode and ABI
2. Configure your deployment script with network settings
3. Execute deployment transaction
4. Contract receives an address on the blockchain

### Deployment Tools

| Tool | Description |
|------|-------------|
| [Hardhat](https://hardhat.org/guides/deploying.html) | Script-based deployment with plugins |
| [Truffle](https://www.trufflesuite.com/docs/truffle/advanced/networks-and-app-deployment) | Migration-based deployment |
| [Remix](https://remix.ethereum.org) | Browser-based deployment |
| [Tenderly](https://tenderly.co/) | Deployment with monitoring |

## Development Workflow

1. **Setup** - Choose a framework and configure your project
2. **Write** - Develop smart contracts in Solidity
3. **Compile** - Generate bytecode and ABI
4. **Test** - Run tests on local development network
5. **Deploy to Testnet** - Test on public testnet
6. **Deploy to Mainnet** - Final deployment

## Further Reading

- [Hardhat Documentation](https://hardhat.org/getting-started/)
- [Truffle Documentation](https://www.trufflesuite.com/docs)
- [ABI Specification](https://solidity.readthedocs.io/en/v0.7.0/abi-spec.html)
