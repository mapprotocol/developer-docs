# 如何部署一个MOS

## 找到正确的仓库

```
git clone https://github.com/mapprotocol/mapo-service-contracts.git

cd /mapo-service-contracts/evm/

npm install
```

配置好.env文件

```
* PRIVATE_KEY - 部署地址私钥
* INFURA_KEY - 使用infura的prc的key
* MOS_SALT - 部署mos的salt
* FEE_SALT - 部署feeService的salt
* DEPLOY_FACTORY - Mapo的部署工厂的合约地址0x6258e4d2950757A749a4d4683A7342261ce12471(支持大部分通用的evm链 比如 bsc ploygon eth 主网测试网都支持)
```

## 部署合约

### 部署 MosRelay 合约

以下步骤有助于在 Map 主网或 Makalu 测试网上部署 MOS 中继合约

1. 部署mosRelay

```
npx hardhat relayFactoryDeploy --wrapped <wrapped token> --lightnode <lightNodeManager address> --network <network>
```

- `wrapped token`是 MAP 主网或 MAP Makalu 上WMAP 代币地址。
- `lightNodeManager address`是部署在 MAP 主网或 MAP Makalu 上的轻客户端管理器地址。
2. 部署FeeService合约

```
npx hardhat feeFactoryDeploy --network <network>
```

3. MosRelay设置 FeeService

```
npx hardhat setFeeService  --address <feeService address> --network <network>
```

4. MosRelay 注册 其他mos 地址

```
npx hardhat relayRegisterChain --address <mos contract address> --chain <mos chain id> --type <optional default evm value is 1> --network <network>
```

### EVM链上的MOS

1. 在其他EVM链上部署MOS合约

```
npx hardhat mosFactoryDeploy --wrapped <native wrapped address> --lightnode <lightnode address> --network <network>
```

2. 部署FeeService合约

```
npx hardhat feeFactoryDeploy --network <network>
```

3. 设置 MosRelay 在部署mos合约中

```
npx hardhat mosFactoryDeploy --relay <Relay address> --chain <map chainId> --network <network>
```

4. MOS设置FeeService合约合约地址

```
npx hardhat setFeeService  --address <feeService address> --network <network>
```



## 升级MOS或者MosRelay合约

当通过以下命令升级mos合约时。

请在EVM兼容链上执行以下命令

```
npx hardhat deploy --tags MapoServiceV3Up --network <network>
```

请在Mapo Relay chain主网或Makalu测试网上执行以下命令

```
npx hardhat deploy --tags MapoServiceRelayV3Up --network <network>
```

