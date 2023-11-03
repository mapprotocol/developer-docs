# How to deploy a MOS

## Find the right repository

```
git clone https://github.com/mapprotocol/mapo-service-contracts.git

cd /mapo-service-contracts/evm/

npm install
```

Configure the .env file

```
* PRIVATE_KEY - deploy address private key
* INFURA_KEY - infura key to use prcs
* MOS_SALT - salt for deploying mos
* FEE_SALT - salt for deploying feeService
* DEPLOY_FACTORY - Mapo's deployment factory contract address 0x6258e4d2950757A749a4d4683A7342261ce12471 (supports most common evm chains like bsc polygon eth mainnet testnets)
```

## Deploy contracts

### Deploy MosRelay contract

The following steps help deploy the MOS relay contract on the Map mainnet or Makalu testnet

1. Deploy mosRelay

```
npx hardhat relayFactoryDeploy --wrapped <wrapped token> --lightnode <lightNodeManager address> --network <network>
```

- `wrapped token`is the WMAP token address on MAP mainnet or MAP Makalu.
- `lightNodeManager address`is the light client manager address deployed on MAP mainnet or MAP Makalu.

2. Deploy FeeService contract

```
npx hardhat feeFactoryDeploy --network <network>
```

3. MosRelay sets FeeService

```
npx hardhat setFeeService  --address <feeService address> --network <network>
```

4. MosRelay registers other mos addresses

```
npx hardhat relayRegisterChain --address <mos contract address> --chain <mos chain id> --type <optional default evm value is 1> --network <network>
```

### MOS on EVM chains

1. Deploy MOS contract on other EVM chains

```
npx hardhat mosFactoryDeploy --wrapped <native wrapped address> --lightnode <lightnode address> --network <network>
```

2. Deploy FeeService contract

```
npx hardhat feeFactoryDeploy --network <network>
```

3. Set MosRelay in deploying mos contract

```
npx hardhat mosFactoryDeploy --relay <Relay address> --chain <map chainId> --network <network>
```

4. MOS sets FeeService contract address

```
npx hardhat setFeeService  --address <feeService address> --network <network>
```



## Upgrade MOS or MosRelay contracts

When upgrading the mos contract with the following command.

Please execute the following command on EVM compatible chains

```
npx hardhat deploy --tags MapoServiceV3Up --network <network>
```

Please execute the following command on the Mapo Relay chain mainnet or Makalu testnet

```
npx hardhat deploy --tags MapoServiceRelayV3Up --network <network>
```

