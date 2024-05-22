---
title: integration of MAP with Non-EVM-Compatible Chains
description: 
lang: en
---

# Integration of MAP with Non-EVM-Compatible Chains

The cross-chain process of the Mapo Protocol involves multiple steps, from locking assets to verifying data, ensuring the secure transfer and interoperability of assets across different blockchains. Here, we are only discussing the onboarding process for non-EMV-compatible chains. Completing the development and deployment of the following modules will allow you to integrate with the Mapo Protocol.

## Light-client

Both chains involved in integrating with the Mapo Protocol need to deploy each other's `light clients`. Here, we are discussing how to implement the `Mapo-Relay-Chain`'s `light client` on a target chain that supports smart contracts. Since the target chain supports on-chain smart contracts (wasm or others), so `Mapo-Relay-Chain`'s `light clients` will be implemented in the smart contract language supported by the target chain. In this context, we will primarily introduce some data structures and validation methods for the `Mapo-Relay-Chain`'s `light client` to assist users in implementing this light client in a specific language.


Due to the target chain's deployment of the `Mapo-Relay-Chain`'s `light client`, it only needs to meet one requirement, which is verifying cross-chain messages from the `Mapo-Relay-Chain`. To fulfill this requirement, the `Mapo-Relay-Chain`'s `light client` should provide two functions:

+ Maintain and Update the `Light Client`'s State: This involves storing a certain number of `block headers` and continuously updating to verify new `block headers`.

+ Verify Source Chain Contract Events: This typically involves verifying transaction receipts (Merkle Patricia Tree - MPT verification information) based on the current state of the `light client`.

These functionalities are crucial for the proper operation of the `Mapo-Relay-Chain`'s `light client` on the target chain.

### light-client state

The `light client` can maintain its current state by storing a certain number of consecutive `block headers`. When a user submits a new `block header` to the `light client`, the `light client` can verify the signature of that `block header` to determine its legitimacy. This ensures that the `light client` is continuously updated with valid `block headers`, which are crucial for its proper operation.

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

In the `Map Relay Chain`'s mainnet, validators sign every block, and this signature is stored in the `block header`'s "Extra" field. The `light client` can verify this signature. Additionally, changes in the validator set are not included in the `block header`'s "Extra" field. Therefore, the `light client` can verify the `block header` based on the updated validator set.

The signature data (`Msg`) includes the `hash` of the block header, the consensus `round` that has been reached, and a `MsgCommit` component:

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
The data from the `IstanbulExtra` structure is encoded using RLP and stored in the `header's "Extra"` field. This data includes information about the consensus `round`, BLS signature data (`AggregatedSeal`.`Signature`), and changes in the validator set. The `light client` can use this data to update its state and the validator set.

This process allows the light client to maintain an up-to-date view of the blockchain and verify the headers and transactions effectively. It's a crucial component for cross-chain communication and verification in Mapo Protocol.
[more](https://docs.mapprotocol.io/develop/map-relay-chain/consensus/aggregatedseal)

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

### light-client verify the event

Once the `light client` is updated to the latest state, it can be used to verify cross-chain messages. These messages are often represented using the logs included in on-chain transactions. To prove the validity of a cross-chain message, you need to verify the legitimacy of the transaction receipt in which the message is located. This involves ensuring that the `Merkle Patricia Tree (MPT) root` of the transaction receipt matches the `ReceiptHash` found in the corresponding block header.

To prove the legitimacy of a transaction receipt, you only need to verify that the MPT root of the receipt matches the `ReceiptHash` in the block header. Users can obtain the proof data for a specific transaction and provide both the receipt and the proof data to the deployed `light client`. This will complete the verification of a cross-chain message.

In Mapo Protocol, the `light client` plays a crucial role in ensuring the integrity and validity of cross-chain messages, providing security and trust between different blockchains.


The following obtains the certification data of a transaction receipt:

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

### Maintainer

The Maintainer service is a separate program designed to update and synchronize the `light client`'s state. It submits block header data to the `light client` on both the source and target chains. As the Maintainer service already supports the `Map Relay Chain`, developers of the target chain only need to add support for their chain within the Maintainer service.

Developers of the connecting chain can fork the Map Protocol's provided [Maintainer service](https://github.com/mapprotocol/compass) and make modifications to add support for their own chain. This allows them to tailor the Maintainer service to work seamlessly with their specific blockchain.

+ get the `light-client`'state
+ Retrieve block header data for the corresponding chain based on the current state of the `light client` and submit it to the `light client`

## Mos

The "Mos" layer defines the general message cross-chain framework and implementation logic for the `Mapo protocol`. The Mos layer needs to be deployed on both chains involved in the cross-chain communication. As `mapo-relay-chain` has already implemented this module, developers of the target chain should refer to the following data and interfaces to implement this module.

Main data structure and [interface](https://github.com/mapprotocol/mapo-service-contracts/tree/main/evm/contracts/interface):

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


### Messenger

The `Messenger` service is an independent program designed to monitor and route specific events on the source and target chains involving the `Mos` module. These events include common message events like `mapMessageOut` and `mapMessageIn`. The Messenger service constructs corresponding proof data for these events and ultimately submits cross-chain messages along with proof data to the target chain. Since the Messenger service already supports `map-relay-chain`, developers of the integrating chain only need to add support for their own chain within the Messenger service. Developers can fork a [Messenger service](https://github.com/mapprotocol/compass) provided by Mapo Protocol and customize it to add support for their own chain.

In this process, the Messenger service plays a crucial role in facilitating the transmission of cross-chain messages and their associated proofs, ensuring the secure and reliable transfer of information between the source and target chains. It abstracts the complexity of interacting with smart contracts and handling events, making it easier for developers to integrate their chain into the Mapo Protocol framework.

## Application layer

The application layer represents the true business logic of the cross-chain framework. Users define specific business logic at this layer, such as asset management and operations like lock, unlock, mint, burn, and more. The actual cross-chain operations take place within the application layer, where the `transferOut` interface from the Mos layer is invoked to write specific cross-chain messages onto the chain.

Here's how the cross-chain process flows within the application layer:

+ User Interaction: Users interact with the application layer to initiate cross-chain actions, such as locking, unlocking, or transferring assets between chains.

+ Invoke transferOut: When a user initiates a cross-chain action, the application layer invokes the `transferOut` interface from the `Mos` layer. This interface constructs and formats the cross-chain message, including details of the action to be performed on the target chain.

+ Relay Message: Once the cross-chain message is constructed, the Messenger service is notified. The Messenger service gathers the necessary proof data and submits the cross-chain message along with the proofs to the target chain.

+ Target Chain Verification: On the target chain, the `light-client` deployed for the source chain is used to verify the authenticity and legality of the received cross-chain message. The `light-client` ensures that the data is consistent with the source chain's data, confirming the message's validity.

+ Execution and Action: After successful verification, the application layer on the target chain decodes the received message and performs the corresponding action, such as minting new tokens, unlocking locked assets, etc.

The application layer acts as the bridge between the user's intent and the technical complexities of cross-chain communication. It provides a user-friendly interface where users can trigger cross-chain operations and ensures that these operations are securely executed and verified across the involved chains.






















