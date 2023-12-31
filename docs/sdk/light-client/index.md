# 轻客户端接口

在使用mapo的轻客户端进行交易验证时,我们主要使用的的是verifyProofData方法,我们可以看到verifyProofData需要传入一个bytes作为参数,这是为了
兼容EVM的链进行的统一规范,他实际是一个结构体
```
    struct receiptProof {
        blockHeader header;
        istanbulExtra ist;
        G2 aggPk;
        TxReceiptRlp txReceiptRlp;
        bytes keyIndex;
        bytes[] proof;
    }
    
```

更具体的代码实现请参照[ILightNode](https://github.com/mapprotocol/map-contracts/blob/main/protocol/contracts/interface/ILightNode.sol)

```
// SPDX-License-Identifier: MIT

pragma solidity ^0.8.0;

interface ILightNode {
    // event update block header
    event UpdateBlockHeader(address indexed maintainer, uint256 indexed blockHeight);
    
    // updates the block header
    function updateBlockHeader(bytes memory _blockHeader) external;
    
    // updates the light client
    function updateLightClient(bytes memory _data) external;

    // Verify the validity of the transaction according to the header, receipt
    // The interface will be updated later to return logs
    function verifyProofData(bytes memory _receiptProof) external view returns (bool success, string memory message, bytes memory logs);


    // Get client state
    function clientState() external view returns(bytes memory);
    
    // Get final status information
    function finalizedState(bytes memory _data) external view returns(bytes memory);
    
    // Height of the block that has been synchronized
    function headerHeight() external view returns (uint256 height);
    
    // Gets the range of blocks that can be verified
    function verifiableHeaderRange() external view returns (uint256, uint256);
}
```


