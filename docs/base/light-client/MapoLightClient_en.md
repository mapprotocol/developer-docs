# The MAPO light client consists of multiple parts

1. The light client deployed on the Mapo Relay chain synchronizes information from other chains.
2. The light client deployed on other chains synchronizes information from the MAPO chain.



## MAPO Chain's Light Client

The MAPO chain's light client is a light client contract deployed on other chains, used to synchronize information and verify proofs from the Mapo relay chain. The light client has the following functions

```
interface ILightNode{
    event UpdateBlockHeader(address indexed account, uint256 indexed blockHeight);

    //Verify the validity of the transaction according to the header, receipt, and aggPk
    //The interface will be updated later to return logs
    function verifyProofData(bytes memory _receiptProof)
    external
    returns (bool success, string memory message,bytes memory logsHash);

    //Validate headers and update validation members
    function updateBlockHeader(blockHeader memory bh,istanbulExtra memory ist, G2 memory aggPk) external;

    //Initialize the first validator
    function initialize(
        //The total weight of votes
        uint256 _threshold,
        //committee members
        address[] memory validators,
        //G1 public key corresponding to the committee member
        G1[] memory _pairKeys,
        //Weights corresponding to committee members
        uint256[] memory _weights,
        //number of committees
        uint epoch,
        //The number of blocks corresponding to each committee
        uint epochSize,
        address verifyTool)
    external;

    function verifiableHeaderRange() external view returns (uint256, uint256);
}
```

As a contract deployed on other chains to synchronize MAPO chain information, the light client completes information synchronization and verification through the following steps

1. After the MAPO light client is deployed, it initializes itself. It can initialize from any epoch on MAPO instead of from the genesis block. Initialization fetches the current epoch size, validator threshold, validator address set, and G1 public keys.
2. After initialization, the light client receives the last block header of the current epoch from an off-chain program called the maintainer. After verifying the block header, the light client gets validator info for the next epoch from the extraData field in the block header and stores it. The epoch is updated to the next epoch.
3. By querying the light client, the verifiable block range can be checked. Within the verifiable range, the block header ECDSA signature is verified, the block header BLS signature is verified through aggregate G2 public keys, and the MPT built from all receipts in the block is verified. If these all pass, the data is proven valid.
For specific verification details, refer to [MAPO EVM Chain Light Client](https://docs.mapprotocol.io/develop/light-client/mapo-light-client/evm),With the same core verification process, the MAPO chain light client not only supports deployment on some EVM-compatible chains, but now also supports light client synchronization and verification of the MAPO chain by these heterogeneous chains. See [MAPO Near Chain Light Client](https://docs.mapprotocol.io/develop/light-client/mapo-light-client/near)

## Other Chains' Light Clients

Other chains' light clients are light client contracts deployed on the Mapo Relay chain to synchronize information and verify proofs from the corresponding chain. The light clients need to implement the following functions
```
interface ILightNode {

    event UpdateBlockHeader(address indexed maintainer, uint256 indexed blockHeight);

    function updateBlockHeader(bytes memory _blockHeader) external;

    function updateLightClient(bytes memory _data) external;

    // Verify the validity of the transaction according to the header, receipt
    // The interface will be updated later to return logs
    function verifyProofData(bytes memory _receiptProof) external view returns (bool success, string memory message, bytes memory logs);

    // Get client state
    function clientState() external view returns(bytes memory);

    function finalizedState(bytes memory _data) external view returns(bytes memory);

    function headerHeight() external view returns (uint256 height);

    function verifiableHeaderRange() external view returns (uint256, uint256);
}
```

All light client contracts on the Mapo Relay chain are registered with the LightClientManager contract, which is responsible for their unified management. The LightClientManager is a managing contract that depends on the contract addresses of other light clients registered by the owner on the MAPO Relay chain. Detailed verification logic and block data updates still reside within their respective light client contracts. The LightClientManager is primarily designed to provide a more convenient way to update blocks and perform proof verification. Now, let's take a look at the method interfaces of the LightClientManager. With these common methods, synchronizing blocks and verifying transactions on the Mapo Relay chain will become more convenient and efficient.
```
interface ILightClientManager {
    function updateBlockHeader(uint256 _chainId, bytes memory _blockHeader) external;

    function updateLightClient(uint256 _chainId, bytes memory _data) external;

    function verifyProofData(uint256 _chainId, bytes memory _receiptProof) external
    view returns (bool success, string memory message,bytes memory logs);

    function clientState(uint256 _chainId) external view returns(bytes memory);

    function headerHeight(uint256 _chainId) external view returns (uint256);

    function verifiableHeaderRange(uint256 _chainId) external view returns (uint256, uint256);

    function finalizedState(uint256 _chainId,bytes memory _data) external view returns(bytes memory);
}

```

Specific details regarding the updating of block headers and proof verification for the corresponding chain can be found in

[BNB Smart Chain](https://docs.mapprotocol.io/develop/light-client/light-clients/bsc)

[Near Protocol](https://docs.mapprotocol.io/develop/light-client/light-clients/near)

[Polygon](https://docs.mapprotocol.io/develop/light-client/light-clients/matic)

[Ethereum2.0](https://docs.mapprotocol.io/develop/light-client/light-clients/eth2)

[Klaytn](https://docs.mapprotocol.io/develop/light-client/light-clients/klaytn)

