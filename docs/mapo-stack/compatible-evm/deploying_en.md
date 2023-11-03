---
title: Deploying smart contracts
description:
lang: en
---

You need to deploy your smart contract for it to be available to users of an `MAPO-Relay-Chain`. The following `MAPO-Relay-Chain` is collectively referred to as MAPO.

To deploy a smart contract, you merely send an MAPO coin transaction containing the compiled code of the smart contract without specifying any recipient.

## prerequisites

You should understand [MAPO networks](/docs/base/mapo-relay-chain/nodes/architecture_en.md), [transactions](/docs/base/transactions/index_en.md) and the [anatomy of smart contracts](docs/mapo-stack/compatible-evm/anatomy_en.md) before deploying smart contracts.

Deploying a contract also costs MAPO coins (MAPO) since they are stored on the blockchain, so you should be familiar with [gas and fees](/docs/base/gas/index_en.md) on MAPO.

Finally, you'll need to compile your contract before deploying it, so make sure you've read about [compiling smart contracts](/docs/mapo-stack/compatible-evm/compile_en.md).

## how-to-deploy-a-smart-contract

### what-youll-need

- your contract's bytecode – this is generated through [compilation](/docs/mapo-stack/compatible-evm/compile_en.md)
- MAPO coins for gas – you'll set your gas limit like other transactions so be aware that contract deployment needs a lot more gas than a simple MAPO coins transfer
- a deployment script or plugin
- access to an [MAPO node](/docs/base/mapo-relay-chain/public-service.md), either by running your own, connecting to a public node


### steps-to-deploy

The specific steps involved will depend on the tooling you use. For an example, check out the [Hardhat documentation on deploying your contracts](https://hardhat.org/guides/deploying.html) or [Truffle documentation on networks and app deployment](https://www.trufflesuite.com/docs/truffle/advanced/networks-and-app-deployment). These are two of the most popular tools for smart contract deployment, which involve writing a script to handle the deployment steps.

Once deployed, your contract will have an MAPO address.

## related-tools

**Remix - _Remix IDE allows developing, deploying and administering smart contracts for Ethereum like blockchains_**

- [Remix](https://remix.ethereum.org)

**Tenderly - _Web3 development platform that provides debugging, observability, and infrastructure building blocks for developing, testing, monitoring, and operating smart contracts_**

- [tenderly.co](https://tenderly.co/)
- [Docs](https://docs.tenderly.co/)
- [GitHub](https://github.com/Tenderly)
- [Discord](https://discord.gg/eCWjuvt)

**Hardhat - _A development environment to compile, deploy, test, and debug your Ethereum software_**

- [hardhat.org](https://hardhat.org/getting-started/)
- [Docs on deploying your contracts](https://hardhat.org/guides/deploying.html)
- [GitHub](https://github.com/nomiclabs/hardhat)
- [Discord](https://discord.com/invite/TETZs2KK4k)

**Truffle -** **_A development environment, testing framework, build pipeline, and other tools._**

- [trufflesuite.com](https://www.trufflesuite.com/)
- [Docs on networks and app deployment](https://www.trufflesuite.com/docs/truffle/advanced/networks-and-app-deployment)
- [GitHub](https://github.com/trufflesuite/truffle)