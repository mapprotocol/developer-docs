---
title: Map 对接EVM兼容链
description: 
lang: zh
---

# Map 对接EVM兼容链

Mapo Protocol的跨链过程涉及多个步骤，从锁定资产到验证数据，确保资产在不同区块链之间的安全传输和互通,这里我们仅讨论EMV兼容链的接入过程。完成以下几个模块的开发和部署即可接入到mapo protocol：

## Light-client层

接入mapo Protocol协议的双方链都需要部署对方的`light-client`，由于双方链都是兼容EVM，所以这里双方`light-client`都将以solidity实现，这样可以减少双方链主网的升级与`light-client`的维护。为了在接入链上部署`mapo-relay-chain`的`light-client`合约，需要接入链支持bls,bn254等预编译指令。

由于接入链与Mapo Protocol上的其他链的消息跨链都是通过`map-relay-chain`作为中转，所以接入链只需要部署`map-relay-chain`的`light-client`就可以实现来自于`map-relay-chain`的跨链消息的验证。由于`map-relay-chain`已经实现了solidity版本的`light-client`，故接入链只需要专注于实现自己的solidity的`light-client`, 接入链的`light-client`至少需要满足两个功能:

+ 维持和更新`light-client`的状态,即保存一定数量的区块头及持续更新校验新的区块头。
+ 可以根据`light-client`的当前状态，验证源链上的合约事件功能，通常是交易的收据的验证（MPT的验证信息）。

### light-client合约开发:

	为了接入Mapo Protocol协议，接入链方需要满足Mapo Protocol定义的[ILightNode接口](https://github.com/mapprotocol/map-contracts/blob/main/protocol/contracts/interface/ILightNode.sol),其中主要满足以下接口:

```solidity

// Verify the validity of the transaction according to the header, receipt
// The interface will be updated later to return logs

function verifyProofData(bytes memory _receiptProof) external view returns (bool success, string memory message, bytes memory logs);

function updateBlockHeader(bytes memory _blockHeader) external;

function updateLightClient(bytes memory _data) external;

``` 

### 合约部署
   
+ 在map-relay-chain上部署接入链的`light-client`合约.
+ 在接入链上部署map-relay-chain的[light-clinet](https://github.com/mapprotocol/map-contracts/tree/main/mapclients)合约.


### Maintainer开发

Maintainer服务是一个独立的程序,用于更新同步`light-client`的状态,向源链和目标链上的`light-client`提交对应链上的区块头数据. 由于Maintainer服务已经支持了map-relay-chain,所以接入链的开发者只需要在Maintainer服务中增加对自己链的支持即可. 接入链的开发者可以fork一个mapo protocol提供的[Maintainer](https://github.com/mapprotocol/compass)做二次开发以增加对自己链的支持.

+ 获取当前`light-client`的状态
+ 根据当前`light-client`的状态获取对应链的区块头数据并提交到`light-client`


## Mos层

Mos层定义了mapo protocol通用消息跨链的框架结构及实现逻辑，接入链方的开发者不需要再实现该模块，而可以直接部署使用，Mos层需要在跨链双方的链上都部署，由于是EVM兼容链，这里我们实现的Mos是solidity版本，其安装部署流程参考[这里](https://github.com/mapprotocol/mapo-service-contracts/blob/main/evm/README.md).

主要数据结构和[接口](https://github.com/mapprotocol/mapo-service-contracts/tree/main/evm/contracts/interface):

```solidity
 enum MessageType {
        CALLDATA,
        MESSAGE
    }

    // @notice This is the configuration you need across the chain.
    // @param relay - When it is true, the relay chain is required to perform a special execution to continue across the chain.
    // @param msgType - Different execution patterns of messages across chains.
    // @param target - The contract address of the target chain.
    // @param payload - Cross-chain content.
    // @param gasLimit - The gasLimit allowed to be consumed by an operation performed on the target chain.
    // @param value - Collateral value cross-chain, currently not supported, default is 0.
    struct MessageData {
        bool relay;
        MessageType msgType;
        bytes target;
        bytes payload;
        uint256 gasLimit;
        uint256 value;
    }

    // @notice Gets the fee to cross to the target chain.
    // @param toChain - Target chain chainID.
    // @param feeToken - Token address that supports payment fee,if it's native, it's address(0).
    // @param gasLimit - The gasLimit allowed to be consumed by an operation performed on the target chain.
    function getMessageFee(uint256 toChain, address feeToken, uint256 gasLimit) external view returns(uint256, address);


    // @notice Initiate cross-chain transactions. Generate cross-chain logs.
    // @param toChain - Target chain chainID.
    // @param messageData - Structure MessageData encoding.
    // @param feeToken - In what Token would you like to pay the fee.
    function transferOut(uint256 toChain, bytes memory messageData, address feeToken) external payable  returns(bytes32);


    // @notice Add the fromaddress permission.
    // @param fromChain - The chainID of the source chain.
    // @param fromAddress - The call address of the source chain.
    // @param tag - Permission,false: revoke permission.
    function addRemoteCaller(uint256 fromChain, bytes memory fromAddress, bool tag) external;

    // @notice Query whether the contract has execution permission.
    // @param mosAddress - This is the mos query address.
    // @param fromChainId - The call chain id of the source chain.
    // @param fromAddress - The call address of the source chain.
    function getExecutePermission(address mosAddress,uint256 fromChainId,bytes memory fromAddress) external view returns(bool);

    event mapMessageOut(uint256 indexed fromChain, uint256 indexed toChain, bytes32 orderId, bytes fromAddrss, bytes callData);

    event mapMessageIn(uint256 indexed fromChain, uint256 indexed toChain, bytes32 orderId, bytes fromAddrss, bytes callData, bool result, bytes reason);
```


### mos合约部署
   
在map-relay-chain上部署mos合约:
+ 下载[mos库](https://github.com/mapprotocol/mapo-service-contracts)。
+ 前往evm文件夹。
+ 按照[README](https://github.com/mapprotocol/mapo-service-contracts/blob/main/evm/README.md)中提供的部署说明部署mos合约。
  
在接入链上部署mos合约:   
+ 下载[mos库](https://github.com/mapprotocol/mapo-service-contracts)。
+ 前往evm文件夹。
+ 按照[README](https://github.com/mapprotocol/mapo-service-contracts/blob/main/evm/README.md)中提供的部署说明部署mos合约。

mos合约部署后可以通过`setLightClient`方法设置对应`light-client`合约地址.


### Messeager程序开发

Messenger服务是一个独立的程序，旨在监控并路由源链和目标链上`mos合约`的特定事件。这些事件包括常见的消息事件，如`mapMessageOut`和`mapMessageIn`。Messenger服务为这些事件构建相应的证明数据，并最终将跨链消息以及证明数据提交到目标链。由于Messenger服务已经支持`map-relay-chain`，集成链的开发者只需要在Messenger服务中添加对自己链的支持。开发者可以fork一个Mapo Protoco提供的[Messenger服务](https://github.com/mapprotocol/compass)，并自定义以添加对自己链的支持。

在这个过程中，Messenger服务在促进跨链消息及其相关证明的传输中扮演关键角色，确保信息在源链和目标链之间安全可靠地传输。它抽象了与智能合约交互和处理事件的复杂性，使开发者更容易将自己的链集成到Mapo Protocol框架中。

## 应用层

应用层代表了跨链框架的真正业务逻辑。用户在该层定义具体的业务逻辑，如资产管理以及锁定、解锁、铸造、销毁等操作。实际的跨链操作发生在应用层中，其中会调用Mos层的transferOut接口，将具体的跨链消息写入链上。

以下是应用层内的跨链过程流程：

+ 用户交互：用户与应用层进行交互，启动跨链操作，如在链之间锁定、解锁或转移资产。

+ 调用transferOut：当用户启动跨链操作时，应用层会调用`Mos`层的`transferOut`接口。该接口构建和格式化跨链消息，包括在目标链上执行的操作的细节。

+ 路由消息：一旦跨链消息构建完成，Messenger服务会被通知(监听)。Messenger服务收集必要的证明数据，并将跨链消息与证明一起提交到目标链。

+ 目标链验证：在目标链上，使用为源链部署的`light-client`来验证接收到的跨链消息的真实性和合法性。`light-client`确保数据与源链的数据一致，确认消息的有效性。

+ 执行和操作：在成功验证后，目标链上的应用层解码接收到的消息并执行相应的操作，如铸造新代币、解锁锁定的资产等。

应用层充当用户意图与跨链通信的技术复杂性之间的桥梁。它提供了用户友好的界面，让用户触发跨链操作，并确保这些操作在涉及的链之间安全执行和验证。






















