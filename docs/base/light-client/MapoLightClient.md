| title        | description | lang |
| ------------ | ----------- | ---- |
| MAPO轻客户端 |             | zh   |

MAPO轻客户端是由量大部分组成

1. 部署在Mapo Relay chain上的轻客户端,用来同步其他链信息的轻客户端
2. 部署在其他链上用来同步MAPO 链信息的轻客户端



## MAPO链的轻客户

MAPO链的轻客户端是在其他链上部署的轻客户端合约,用来同步Mapo relay chain的信息和proof的验证,轻客户端有如下功能方法

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

作为部署在在其他链上用来同步MAPO链信息的合约,通过以下几步来完成轻客户端的信息同步和验证

1. MAPO轻客户端部署完成后会进行初始化,可以从MAPO的任意一个纪元开始初始化,而不用从创世区块开始,初始化会包扣当前纪元大小,验证器阈值,验证者地址集和G1 publickeys
2. 初始化完成后,轻客户端从称为维护者的链下程序接收当前纪元的最后一个区块头,在通过区块头验证后,轻客户端从区块头中的extraData字段中获取下一个纪元的验证者信息然后储存在轻客户端中,将纪元更新为下一个纪元
3. 通过查询轻客端户端可验证区块范围,在可验证范围内,通过区块头ECDSA签名,通过聚合G2公钥验证区块头BLS签名并且通过区块中所有收据构建的MPT验证,这些验证都通过则证明数据有效

具体的验证详情可以参考[MAPO EVM Chain Light Client](https://docs.mapprotocol.io/develop/light-client/mapo-light-client/evm),同样的核心验证过程,MAPO链的轻客户不仅支持在一些兼容EVM链上进行部署,现在也支持了这类异构链进行MAPO链的轻客户同步和验证,具体详情可以查看[ MAPO Near Chain Light Client](https://docs.mapprotocol.io/develop/light-client/mapo-light-client/near)

## 其他链的轻客户端

其他链的轻客户端是在Mapo Relay chain上部署的轻客户端合约,用来同步相对应链的信息和proof验证,轻客户端需要实现如下功能方法

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

在Mapo Relay chain上的轻客户端合约都会注册到LightClientManager合约上,由LightClientManager统一管理,LightClientManager是一个管理合约,依赖于所有者在 MAPO  Relay chain上注册其他轻客户端的合约地址。详细的验证逻辑和区块数据仍在各自轻客户端合约上更新。 LightClientManager只是为了提供更方便的更新区块和Proof验证,下面我来看看LightClientManager的方法接口,有了这些通用方法,我们在Mapo Relay chain上进行区块的同步和交易的验证就会更加的方便快捷

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

具体的对应链的区块头更新和proof验证的详情请参考

[BNB Smart Chain](https://docs.mapprotocol.io/develop/light-client/light-clients/bsc)

[Near Protocol](https://docs.mapprotocol.io/develop/light-client/light-clients/near)

[Polygon](https://docs.mapprotocol.io/develop/light-client/light-clients/matic)

[Ethereum2.0](https://docs.mapprotocol.io/develop/light-client/light-clients/eth2)

[Klaytn](https://docs.mapprotocol.io/develop/light-client/light-clients/klaytn)

