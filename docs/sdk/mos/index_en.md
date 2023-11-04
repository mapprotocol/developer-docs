# MOS interface

MOSV2 is a token cross-chain service that operates on EVM-compatible ecosystems. It primarily offers four token cross-chain modes:
- transferOutToken This mode is used to initiate cross-chain requests for transferring specific tokens to the target chain.
- transferOutNative It is used to initiate cross-chain requests for transferring native tokens of the source chain to the target chain.
- depositToken In this mode, tokens are transferred from the source chain to the target chain for staking or collateralization.
- depositNative Native tokens of the source chain are transferred to the target chain for staking or collateralization in this mode.

Please refer to a more detailed code implementation for specifics [IMOSV2](https://github.com/mapprotocol/map-contracts/blob/main/mos/evmv2/contracts/interface/IMOSV2.sol)

If you want to perform cross-chain operations using MOSV2, you can use the following interfaces

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

MOSV3 is a message-based cross-chain service that primarily operates in EVM-compatible ecosystems. It is responsible for transferring information from the source chain to the target chain for message updates and execution. It not only supports cross-chain token transfers but also enables the update of contract information.
- transferOut It is primarily responsible for popping cross-chain message logs and has messengers for executing message delivery on the target chain.
- getMessageFee Its main responsibility is to estimate the fees for cross-chain transactions from the source chain to the target chain.

Please refer to a more detailed code implementation for specifics[IMOSV3](https://github.com/mapprotocol/mapo-service-contracts/blob/main/evm/contracts/interface/IMOSV3.sol)
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