# MAPO Protocol Development documentation
> [Chinese](https://mapo.gitbook.io/docs-zh/)

This document aims to help you understand MAPO, how to use it to build decentralized applications, and how to interconnect a blockchain with MAPO. The document introduces the concept of MAPO, explains the MAPO technology stack, and provides use cases for MAPO applications.

Based on open-source community guidelines, you can propose new topics, add new content, and provide examples where you think it would be helpful. All documents can be edited through GitHub and are stored on decentralized storage facilities like `Arweave`. If you are unsure about how to proceed, please follow the [instructions](docs/editing-markdown.md).

If this is your first time diving into MAPO development, it is recommended that you start from the beginning. This will not only familiarize you with MAPO but also delve into the underlying blockchain technologies and concepts like ZK, providing you with a new understanding of peer-to-peer code trust.


## Base

+ [MAPO Introduction](docs/base/intro-to-mapo/index_en.md)-----MAPO brief introduction
+ [MAPO coins](docs/base/intro-to-mapo/mapo-coin_en.md)-----MAPO coins brief introduction
+ [Full chain DAPP](docs/base/omnichain-dapp/index_en.md)------ Introduction to dapp covering various blockchains
+ [Differences Between Full-Chain Applications and Single or Multi-Chain Applications](docs/base/omnichain-dapp/different_en.md)
+ [Differences Between Third-Party Trusted Cross-Chain and Peer-to-Peer Cross-Chain Solutions](docs/base/omnichain-dapp/the-other_en.md)
+ [BTC layer2](docs/btc-layer2/index_en.md)
  + [brc-201](docs/btc-layer2/brc201.md)
+ [Account](docs/base/accounts/index_en.md) 
+ [Transactions](docs/base/transactions/index_en.md) 
+ [block](docs/base/block/index_en.md)
+ [MPT tree](docs/base/mpt/index_en.md) 
+ [RLP](docs/base/rlp/index_en.md)
+ [Gas fee](docs/base/gas/index_en.md)
+ [Cross Chain Message](docs/base/cross-chain-message/index_en.md)
+ [light client](docs/base/light-client/index_en.md)
  + [MAPO light client](docs/base/light-client/MapoLightClient_en.md) 
+ [MOS](docs/base/mos/index_en.md)
    + [MOS interface and functions](docs/base/mos/mos_interface_en.md)
    + [deploy MOS](docs/base/mos/mos_deploy_en.md)
    + [Messenger](docs/base/mos/Messenger_en.md) 
+ [map-relay-chain(atlas)](docs/base/mapo-relay-chain/nodes/architecture_en.md)
    + atlas architecture
        + [atlas architecture](docs/base/mapo-relay-chain/nodes/architecture_en.md)
        + atlas genesis
          + [genesis config](docs/base/mapo-relay-chain/nodes/genesis-config_en.md)
          + [genesis contract](/docs/base/mapo-relay-chain/genesis-contract/index_en.md)
            + ABI
              + [Accounts](docs/base/mapo-relay-chain/genesis-contract/accounts_en.md)
              + [Election](docs/base/mapo-relay-chain/genesis-contract/election_en.md)
              + [EpochRewards](docs/base/mapo-relay-chain/genesis-contract/epoch-rewards_en.md)
              + [LockedGold](docs/base/mapo-relay-chain/genesis-contract/locked-gold_en.md)
              + [Validators](docs/base/mapo-relay-chain/genesis-contract/validators_en.md)
            + [address](docs/base/mapo-relay-chain/genesis-contract/address_en.md)
            + [deploy](docs/base/mapo-relay-chain/genesis-contract/deploy_en.md)
        + [precompile-contract](docs/base/mapo-relay-chain/precompile-contract_.md)
        + protocol
          + [Proof of Stake](docs/base/mapo-relay-chain/protocol/pos_en.md)
          + [consensus](docs/base/mapo-relay-chain/protocol/consensus_en.md)
          + [election](docs/base/mapo-relay-chain/protocol/election_en.md)
          + [rewards](docs/base/mapo-relay-chain/protocol/rewards_en.md)
          + [governance](docs/base/mapo-relay-chain/protocol/governance_en.md)
    + deploy atlas
      + [run atlas](docs/base/mapo-relay-chain/nodes/run-a-node_en.md)
      + [run atlas(archive)](docs/base/mapo-relay-chain/nodes/archive-nodes_en.md)
      + [run atlas(bootnodes)](docs/base/mapo-relay-chain/nodes/bootnodes_en.md)
      + [run atlas(validator)](docs/base/mapo-relay-chain/nodes/validator-nodes_en.md)
      + [run atlas（RPC）](docs/base/mapo-relay-chain/nodes/rpc-nodes_en.md)
    + [Marker tool](docs/base/mapo-relay-chain/marker/overview_en.md)
      + [Genesis](docs/base/mapo-relay-chain/nodes/genesis-config_en.md) 
      + [Validator](docs/base/mapo-relay-chain/marker/validator_en.md) 
      + [Vote](docs/base/mapo-relay-chain/marker/vote_en.md) 
      + [Common](docs/base/mapo-relay-chain/marker/common_en.md)
    + [make private network(atlas)](docs/base/mapo-relay-chain/make-private-network_en.md)
    + public service   
      + [public network](docs/base/mapo-relay-chain/public-service_en.md)
    + example
      + [how-to-become-a-new-validator](docs/base/mapo-relay-chain/example/how-to-become-a-new-validator_en.md)
      + [how-to-become-a-new-validator(advanced)](docs/base/mapo-relay-chain/example/how-to-become-a-new-validator-advanced_en.md)
+ [Compass(maintainer，messenger)](docs/base/Compass/index_en.md)
    + [Compass - arch and model](docs/base/Compass/index_en.md#compass---the-introduction-of-model-and-arch)
    + [Compass - config](docs/base/Compass/index_en.md#config-of-compass)
    + [Compass - deploy](docs/base/Compass/index_en.md#compass-env-and-deploy)
    + [Compass secondary development - define your own routing service based on compass](docs/base/Compass/index_en.md#compass-secondary-development---define-your-own-routing-service-based-on-compass)

## MAPO Stack

+ [stack](docs/mapo-stack/stack/index.md)
+ [Compatible-EVM](docs/mapo-stack/compatible-evm/index_en.md)
  + [Smart Contracts Language](docs/mapo-stack/compatible-evm/solidity_en.md)
  + [Smart Contracts Anatomy](docs/mapo-stack/compatible-evm/anatomy_en.md)
  + [Smart Contracts Libraries](docs/mapo-stack/compatible-evm/libraries_en.md)
  + [Smart Contracts Compile](docs/mapo-stack/compatible-evm/compile_en.md)
  + [Smart Contracts Testing](docs/mapo-stack/compatible-evm/testing_en.md)
  + [Smart Contracts Deploy](docs/mapo-stack/compatible-evm/deploying_en.md)
  + [Smart Contracts Composability](docs/mapo-stack/compatible-evm/composability_en.md)
  + [Smart Contracts Security](docs/mapo-stack/compatible-evm/security_en.md)
  + [Formal-Verification](docs/mapo-stack/compatible-evm/formal-verification_en.md)
  + [Frameworks](docs/mapo-stack/compatible-evm/frameworks_en.md)
  + [dev-network](docs/mapo-stack/compatible-evm/dev-network_en.md)
+ [MAPO Implement Cross-chain Interoperability](docs/mapo-stack/chains-connect/index_en.md)
  + [integration of MAP with EVM-Compatible Chains](docs/mapo-stack/chains-connect/evm-chain/index_en.md)
    + [light client verify](docs/mapo-stack/chains-connect/evm-chain/index_en.md#light-client)
    + [light client update state](docs/mapo-stack/chains-connect/evm-chain/index_en.md#maintainer) 
      + [maintainer support the new chain](docs/mapo-stack/chains-connect/evm-chain/index_en.md#maintainer) 
      + [deploy maintainer](docs/base/Compass/index_en.md#compass-env-and-deploy) 
    + [MOS](docs/mapo-stack/chains-connect/evm-chain/index_en.md#mos)
      + [MOS development](docs/mapo-stack/chains-connect/evm-chain/index_en.md#mos-contract-development)
      + [Messeager Development](docs/mapo-stack/chains-connect/evm-chain/index_en.md#messeager-development) 
  + [integration of MAP with Non-EVM-Compatible Chains](docs/mapo-stack/chains-connect/non-evm-chain/index_en.md)
    + [light client verify](docs/mapo-stack/chains-connect/non-evm-chain/index_en.md#light-client)
    + [light client update state](docs/mapo-stack/chains-connect/non-evm-chain/index_en.md#maintainer)
    + [MOS](docs/mapo-stack/chains-connect/non-evm-chain/index_en.md#mos) 
      + [Messenger](docs/mapo-stack/chains-connect/non-evm-chain/index_en.md#messeager)
+ [How to develop cross-chain applications](docs/mapo-stack/omni-dapp/index.md)
+ SDK/API 
  +  [MOS interface](docs/sdk/mos/index_en.md)
  +  [Light client interface](docs/sdk/light-client/index_en.md)
  +  Atlas RPC
     +  [json-rpc](docs/sdk/mapo-relay-chain/json-rpc/index_en.md)
        +  [atlas json rpc](docs/sdk/mapo-relay-chain/json-rpc/atlas-json-rpc.md)
        +  [atlas consensus rpc](docs/sdk/mapo-relay-chain/json-rpc/atlas-consensus-rpc.md)
     +  [javaScript sdk](docs/sdk/mapo-relay-chain/javaScript.md)
     +  [go-sdk](/docs/sdk/mapo-relay-chain/go-sdk_en.md)
  + Backend API
    + [SCAN API](docs/sdk/backend/index.md)


## Zero-Knowledge Proof

* [zk proof](docs/zk/index_en.md)



