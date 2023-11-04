---
title: Map 对接非EVM兼容链
description: 
lang: zh
---

# Map 对接非EVM兼容链

Mapo Protocol的跨链过程涉及多个步骤，从锁定资产到验证数据，确保资产在不同区块链之间的安全传输和互通,这里我们仅讨论非EMV兼容链的接入过程。完成以下几个模块的开发和部署即可接入到mapo protocol.

## Light-client层

接入mapo Protocol协议的双方链都需要部署对方的`light-client`，这里我们仅讨论如何在支持智能合约的目标链上实现`mapo-relay-chain`的`light-client`，由于目标链支持链上的智能合约(wasm或其他)，所以`mapo-relay-chain`的`light-client`都将以目标链支持的智能合约语音来实现，这里我们主要介绍`mapo-relay-chain`的`light-client`的一些数据结构和验证方法，以方便用户使用特定语音去实现该`light-client`。

由于目标链上部署的`map-relay-chain`的`light-client`只需要满足一个需求，就是对来自于`map-relay-chain`的跨链消息的验证。为了实现该需求，`map-relay-chain`的`light-client`需要满足两个功能:

+ 维持和更新`light-client`的状态,即保存一定数量的`区块头`及持续更新校验新的`区块头`。
+ 可以根据`light-client`的当前状态，验证源链上的合约事件功能，通常是交易的收据的验证（MPT的验证信息）。


### light-client状态

`light-client`中可以保存一定数量连续的`区块头`用于维持当前`light-client`的状态，用户向`light-client`提交新的`区块头`，`light-client`可以验证该`区块头`的签名来判断该`区块头`的合法性。

```golang
// Header represents a block header in the Ethereum blockchain.
type Header struct {
	ParentHash  common.Hash    `json:"parentHash"       gencodec:"required"`
	Coinbase    common.Address `json:"miner"            gencodec:"required"`
	Root        common.Hash    `json:"stateRoot"        gencodec:"required"`
	TxHash      common.Hash    `json:"transactionsRoot" gencodec:"required"`
	ReceiptHash common.Hash    `json:"receiptsRoot"     gencodec:"required"`
	Bloom       Bloom          `json:"logsBloom"        gencodec:"required"`
	Number      *big.Int       `json:"number"           gencodec:"required"`
	GasLimit    uint64         `json:"gasLimit"         gencodec:"required"`
	GasUsed     uint64         `json:"gasUsed"          gencodec:"required"`
	Time        uint64         `json:"timestamp"        gencodec:"required"`
	Extra       []byte         `json:"extraData"        gencodec:"required"`
	MixDigest   common.Hash    `json:"mixHash"`
	Nonce       BlockNonce     `json:"nonce"`

	// BaseFee was added by EIP-1559 and is ignored in legacy headers.
	BaseFee *big.Int `json:"baseFeePerGas" rlp:"optional"`
}
```
在`map-relay-chain`的主网上的验证者会对每一个区块都签名，并且该签名会保存在`区块头`里（header.Extra）,`light-client`可以通过验证该签名，同时`map-relay-chain`的验证者集合的变化结果也不会保存在`区块头`（header.Extra）里，所以`light-client`可以根据更新的验证者集合来验证`区块头`.

签名数据`Msg`包含了区块头的`hash`,达成共识的`round`以及一个`MsgCommit`:

```golang
// hash: header.Hash()
func PrepareCommittedSeal(hash common.Hash, round *big.Int) []byte {
	var buf bytes.Buffer
	buf.Write(hash.Bytes())
	buf.Write(round.Bytes())
	buf.Write([]byte{byte(istanbul.MsgCommit)})
	return buf.Bytes()
}
```

`IstanbulExtra`结构的数据经过rlp编码后存入`header.Extra`字段，其中就包含了达成共识的`round`数据，bls的签名数据`AggregatedSeal`.`Signature`以及变化的验证者,`light-client`可以根据这些数据更新其状态及验证者集合. [更多信息](https://docs.mapprotocol.io/develop/map-relay-chain/consensus/aggregatedseal)

```golang
type IstanbulExtra struct {
	// AddedValidators are the validators that have been added in the block
	AddedValidators []common.Address
	// AddedValidatorsPublicKeys are the BLS public keys for the validators added in the block
	AddedValidatorsPublicKeys []blscrypto.SerializedPublicKey
	// AddedValidatorsG1PublicKeys are the BLS public keys for the validators added in the block
	AddedValidatorsG1PublicKeys []blscrypto.SerializedG1PublicKey
	// RemovedValidators is a bitmap having an active bit for each removed validator in the block
	RemovedValidators *big.Int
	// Seal is an ECDSA signature by the proposer
	Seal []byte
	// AggregatedSeal contains the aggregated BLS signature created via IBFT consensus.
	AggregatedSeal IstanbulAggregatedSeal
	// ParentAggregatedSeal contains and aggregated BLS signature for the previous block.
	ParentAggregatedSeal IstanbulAggregatedSeal
}

type IstanbulAggregatedSeal struct {
	// Bitmap is a bitmap having an active bit for each validator that signed this block
	Bitmap *big.Int
	// Signature is an aggregated BLS signature resulting from signatures by each validator that signed this block
	Signature []byte
	// Round is the round in which the signature was created.
	Round *big.Int
}
```

### light-client验证消息

一旦`light-client`更新到最新状态了就可以用于验证跨链消息了，通常使用链上交易中输出的日志来表示一个跨链消息，所以我们只需要验证消息所在的交易收据的合法性，即可证明该跨链消息的有效性.

证明交易收据的合法性只需要验证该交易收据所在的MPT树的root与对应的`区块头`里的`ReceiptHash`一致即可，则用户可以获取某交易的证明数据，并将收据及证明数据传递到已部署的`light-client`即可完成一个跨链消息的验证.

如下获取一条交易收据的证明数据:

```golang
func getTxProve() []byte {
	// get receipts from the node
	conn := dialConn()
	txsHash := getTransactionsHashByBlockNumber(conn, blockNumber)
	receipts := getReceiptsByTxsHash(conn, txsHash)
	// get receipts from json
	//receipts := GetReceiptsFromJSON()

	tr, err := trie.New(common.Hash{}, trie.NewDatabase(memorydb.New()))
	if err != nil {
		panic(err)
	}

	tr = atlastypes.DeriveTire(receipts, tr)
	proof := light.NewNodeSet()
	key, err := rlp.EncodeToBytes(txIndex)
	if err != nil {
		panic(err)
	}
	if err = tr.Prove(key, 0, proof); err != nil {
		panic(err)
	}
	txProve := TxProve{
		Tx: &TxParams{
			From:  fromAddr.Bytes(),
			To:    toAddr.Bytes(),
			Value: SendValue,
		},
		Receipt:     receipts[txIndex],
		Prove:       proof.NodeList(),
		BlockNumber: blockNumber.Uint64(),
		TxIndex:     txIndex,
	}

	input, err := rlp.EncodeToBytes(txProve)
	if err != nil {
		panic(err)
	}
	return input
}
```

### Maintainer开发

Maintainer服务是一个独立的程序,用于更新同步`light-client`的状态,向源链和目标链上的`light-client`提交对应链上的区块头数据. 由于Maintainer服务已经支持了map-relay-chain,所以目标链的开发者只需要在Maintainer服务中增加对自己链的支持即可. 接入链的开发者可以fork一个mapo protocol提供的[Maintainer](https://github.com/mapprotocol/compass)做二次开发以增加对自己链的支持.

+ 获取当前`light-client`的状态
+ 根据当前`light-client`的状态获取对应链的区块头数据并提交到`light-client`


## Mos层

Mos层定义了mapo protocol通用消息跨链的框架结构及实现逻辑，Mos层需要在跨链双方的链上都部署，由于`mapo-relay-chain`上已经实现了该模块，所以目标链的开发者需要参考一下的数据和接口来实现该模块。

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


### Messeager程序开发

Messenger服务是一个独立的程序，旨在监控并路由源链和目标链上`mos`的特定事件。这些事件包括常见的消息事件，如`mapMessageOut`和`mapMessageIn`。Messenger服务为这些事件构建相应的证明数据，并最终将跨链消息以及证明数据提交到目标链。由于Messenger服务已经支持`map-relay-chain`，集成链的开发者只需要在Messenger服务中添加对自己链的支持。开发者可以fork一个Mapo Protoco提供的[Messenger服务](https://github.com/mapprotocol/compass)，并自定义以添加对自己链的支持。

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






















