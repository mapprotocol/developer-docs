# Light client interface

When using the Mapo light client for transaction verification, the primary method we utilize is verifyProofData. It's worth noting that verifyProofData requires a bytes parameter as input. This is done to conform to a unified standard for EVM-compatible chains. In reality, this bytes parameter is a structured data that
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

Please refer to a more detailed code implementation for specifics [ILightNode](https://github.com/mapprotocol/map-contracts/blob/main/protocol/contracts/interface/ILightNode.sol)

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


