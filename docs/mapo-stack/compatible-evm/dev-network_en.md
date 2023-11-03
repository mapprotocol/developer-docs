---
title: MAPO development network
description: Overview of the development network environment and development tools for MAPO.
lang: en
---

The following `MAPO-Relay-Chain` is collectively referred to as MAPO.

When developing a MAPO application using smart contracts, you may want to see how it works locally before deploying it.

This is similar to running a local web server. To test your decentralized application, you can create a local blockchain using a development network. These MAPO development networks offer faster iteration capabilities compared to public testnets.

## what-is-a-development-network

In essence, a development network refers to MAPO clients specifically designed for local development.

### public-testchains

MAPO maintains two public testnets.：MAPO testnet and MAPO dev-net。[more](/docs/base/mapo-relay-chain/public-service.md)。

### ganache

MAPO Virtual Machine is compatible with the Ethereum Virtual Machine (EVM), so it also supports Ganache. You can use Ganache to run tests, execute commands, and inspect state when working with a control chain.

Ganache provides a desktop application (Ganache UI) as well as a command-line tool (`ganache-cli`). It's part of the Truffle suite of tools.

- [web](https://www.trufflesuite.com/ganache)
- [GitHub](https://github.com/trufflesuite/ganache)
- [document](https://www.trufflesuite.com/docs/ganache/overview)

### hardhat-network

MAPO also supports Hardhat. This network allows you to deploy contracts, run tests, and debug your code.

- [web](https://hardhat.org/)
- [GitHub](https://github.com/nomiclabs/hardhat)




