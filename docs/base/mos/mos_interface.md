## MOS是一个消息跨链系统,它主要有两个核心功能

1. 用户通过使用transferOut方法发出跨链请求和弹出跨链日志消息
2. messenger监听到跨链日志,在目标链通过transferIn方法,完成对通过了轻客户验证的跨链消息的执行

MOS的接口如下

```
interface IMOSV3 {

    enum ChainType{
        NULL,
        EVM,
        NEAR
    }

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

}
```

## MOS 在其他EVM链 和 Relay的区别

我们消息跨链流程从来源链 -> Mapo Relay chain-> 目标链这样一个固定的路径,所以 我们把部署再 Mapo Relay chain的MOS 合约称为 MosRelay,MosRelay与其他EVM的MOS最大的区别在于transferIn方法,如果MosRelay不是目标链 transferIn方法会继续弹出跨链日志,以方便messenger监听到目标链进行验证和消息的执行