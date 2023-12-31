# MOS 接口

MOSV2是一个token跨链的服务,在兼容EVM的生态链上主要有4中token款连的模式
- transferOutToken 主要发出一些token的跨链请求到目标链
- transferOutNative 主要发出一些本地链的原生token的跨链请求到目标链
- depositToken 从来源链跨出一些token到目标链进行质押
- depositNative 从来源链跨出一些来源链的原生token到目标链进行质押

具体的代码实现可以参考[IMOSV2](https://github.com/mapprotocol/map-contracts/blob/main/mos/evmv2/contracts/interface/IMOSV2.sol)

想要使用MOSV2进行一些跨链操作,可以使用以下接口

```
// SPDX-License-Identifier: MIT

pragma solidity 0.8.7;

interface IMOSV2 {
    function transferOutToken(address _token, bytes memory _to, uint _amount, uint _toChain) external;
    function transferOutNative(bytes memory _to, uint _toChain) external payable;
    function depositToken(address _token, address to, uint _amount) external;
    function depositNative(address _to) external payable ;


    event mapTransferOut(uint256 indexed fromChain, uint256 indexed toChain, bytes32 orderId,
        bytes token, bytes from, bytes to, uint256 amount, bytes toChainToken);

    event mapTransferIn(uint256 indexed fromChain, uint256 indexed toChain, bytes32 orderId,
        address token, bytes from,  address to, uint256 amount);

    event mapDepositOut(uint256 indexed fromChain, uint256 indexed toChain, bytes32 orderId,
        address token, bytes from, address to, uint256 amount);

}
```

MOSV3是一个消息跨链的服务,在兼容EVM的生态链上主要负责将来源链的信息跨到目标链上进行消息的更新和执行,不仅可以进行token的跨链还能进行合约的信息更新
- transferOut 主要负责弹出跨链消息日志,有messager在目标链上进行传递执行
- getMessageFee 主要负责在来源上进行跨链到目标链上费用的预估

具体的代码实现请查看[IMOSV3](https://github.com/mapprotocol/mapo-service-contracts/blob/main/evm/contracts/interface/IMOSV3.sol)
```
// SPDX-License-Identifier: MIT

pragma solidity ^0.8.0;

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
    // @param payload - Cross-chain data.
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


    // @notice Add remote address permission.
    // @param fromChain - The chain id of the source chain.
    // @param fromAddress - The call address of the source chain.
    // @param tag - Permission,false: revoke permission.
    function addRemoteCaller(uint256 fromChain, bytes memory fromAddress, bool tag) external;

    // @notice Query whether the contract has execution permission.
    // @param targetAddress - The target address.
    // @param fromChain - The chain id of the source chain.
    // @param fromAddress - The call address of the source chain.
    function getExecutePermission(address targetAddress,uint256 fromChain,bytes memory fromAddress) external view returns(bool);

    event mapMessageOut(uint256 indexed fromChain, uint256 indexed toChain, bytes32 orderId, bytes fromAddrss, bytes callData);

    event mapMessageIn(uint256 indexed fromChain, uint256 indexed toChain, bytes32 orderId, bytes fromAddrss, bytes callData, bool result, bytes reason);

}
```