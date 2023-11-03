## Deploying Genesis Contract on Atlas using Truffle

### Introduction to Truffle

Truffle is a world-class development environment, testing framework, and asset pipeline for building decentralized
applications on the Ethereum Virtual Machine (EVM). By creating a Truffle project and configuring some settings, you can
easily deploy your project on the Atlas chain.

To deploy using Truffle on the Atlas relay chain, you need to set up your local environment. If you don't want to deploy
using a local environment, you can use Remix or Replit.

If you are new to Truffle, complete the quickstart tutorial to familiarize yourself with this tool.

### Project Setup

Set up the project directory.

Open a terminal window, create a project directory, and navigate to that directory.

### Initialize Truffle

Initializing Truffle will create a scaffold for your Truffle project.

```shell
truffle init
```

### Configure Deployment Settings

The default truffle.config.js file contains the connection settings required for deployment to the Ethereum network,
imports HDWalletProvider, and connects to the mnemonic in your .env file. To deploy to the Atlas network, you need to
update this configuration file to point to the Atlas network and add some specifics for Atlas best practices.

### Update truffle-config.js File

Open the truffle-config.js file in a text editor and configure its contents as shown in the example below:

```js
  networks: {
    atlas: {
        host: "https://rpc.maplabs.io",
            port
    :
        7445,
            network_id
    :
        "22776"
    }
}
```

### Compile Contracts

```shell
truffle compile
```

### Deploy Contracts

Deploy to the specified Atlas network using the following command:

```shell
truffle deploy --network atlas
```

### Deploy Validator-related Contracts

First, you need to compile the atlas-contracts project. We need the bytecode of atlas-contracts to generate the
genesis.json file.

1. Download the atlas-contracts project in any folder using the following
   command: `git clone https://github.com/mapprotocol/atlas-contracts.git`
2. Assuming you have node installed, switch to the project file, initialize the project, and use the following
   command: `npm install`

3. Download Truffle using `npm install truffle` and compile the project using the Truffle compile
   command: `truffle compile`. After completion, a directory named "build" will be generated in your atlas-contracts
   project. We will use this directory to specify the relevant parameters.