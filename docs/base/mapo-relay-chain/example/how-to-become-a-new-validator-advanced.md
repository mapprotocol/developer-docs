## 如何成为新的 validator 高级篇

为了让您的资产更安全，我们需要您设置一些必要的身份识别参数才能成为 validator 。我们也设置了相应的阈值，以便筛选那些真正想为链做出贡献的人。
当然，我们会给这些人相应的奖励。 以下步骤视为您的第一次操作，因为您只需执行一次以下操作即可成为 validator 。除非您注销验证器或取消
相应的操作，否则您无需执行第二次操作，以免浪费您的 gas 费。

### 第1步：创建账号

在这一步中，您需要将您的身份信息存储到相应的管理合约中，该合约将管理您的账户、密钥和元数据。
此步骤的目的是通过授权用于签名证明、投票、验证替代密钥来确保锁定的 MAPO 更加安全。通过
这样做，您可以在保持访问存储您锁定的 MAPO 的密钥的同时继续参与协议。
您需要 createAccount 命令来执行上述操作，更多关于 createAccount 命令的详细信息请参阅[这里](/docs/base/mapo-relay-chain/marker/common.md#createaccount)。

### 第2步：授杈

授权一个地址代表账号签名共识消息。这个授权地址称为 signer（签名人）。正如他的名字一样，他只负责签名，你的奖
励不会发放给 signer，而是发放给上一步创建的账户，

### 第3步：锁定 MAP

我们设置成为 validator 的阈值是锁定 1,000,000 MAPO 到相应的管理智能合约中。
这部分锁定的 MAPO 将用于以后的惩罚，这也是当选的条件之一。
您需要 LockedMAP 命令来执行上述操作，更多关于 LockedMAP 命令的详细信息请参[这里](/docs/base/mapo-relay-chain/marker/common.md#lockedmap)。

### 第4步： validator 注册

此步骤是注册成为新 validator 的关键步骤。
您需要 register 命令来执行上述操作，更多关于register 命令的详细信息请参阅[这里](/docs/base/mapo-relay-chain/marker/validator.md#register)。
到这一步，您将成功注册为 validator 。接下来，您可以尝试为自己投票。如何投票请参阅[这里](/docs/base/mapo-relay-chain/marker/vote.md#vote)。

### 第5步：投票

validator 必须至少拥有总票数的 0.001 比例才能考虑参加选举。所以 validator 不能没有选票。
我们可以使用我们的 validator 账户为自己投票，也可以让其他 validator 或投票者为自己投票。
我们在第了步中锁定了 1,000,000 MAP，现在为自己投票是一个明智的决定

## 示例

### 启动你的节点

在启动节点之前你需要先构建 atlas，如何构建图集请参阅[这里](/docs/base/mapo-relay-chain/nodes/run-a-node.md#克隆代码仓库并构建)

你需要准备两个 keystore，一个用于质押的称为 account，一个用于参与共识签名区块的 signer。

如果希望 atlas 节点在后台运行而不挂起，可以使用nohup和＆的组合，或者 screen 之类的工具。下面我们将使用 screen 进行演示

`account.json`：account 的 keystore 文件
`signer.json`：signer 的 keystore 文件

`--miner.validator `：指定 signer 的地址
`--port 30321`：确保端口在防火墙上打开

```shell
./atlas --datadir ./node --syncmode "full" --port 30321 --v5disc --mine --miner.validator 0x98efa292822eb7b3045c491e8ae4e82b3b1ac005 --unlock 0x98efa292822eb7b3045c491e8ae4e82b3b1ac005

INFO [08-01|16:15:45.369] Bumping default cache on mainnet         provided=1024 updated=4096
INFO [08-01|16:15:45.370] Maximum peer count                       ETH=50 LES=0 total=50
INFO [08-01|16:15:45.371] Set global gas cap                       cap=50,000,000
INFO [08-01|16:15:45.371] Allocated trie memory caches             clean=614.00MiB dirty=1024.00MiB
INFO [08-01|16:15:45.371] Allocated cache and file handles         database=/Users/t/data/atlas-1/atlas/chaindata cache=2.00GiB handles=5120
INFO [08-01|16:15:45.722] Opened ancient database                  database=/Users/t/data/atlas-1/atlas/chaindata/ancient readonly=false
......

> net
{
  listening: true,
  peerCount: 6,
  version: "22776",
  getListening: function(callback),
  getPeerCount: function(callback),
  getVersion: function(callback)
}

......

INFO [08-01|16:40:40.796] Finalized                                func=Finalize        block=1 epochSize=50000 duration="333.422µs" lastInEpoch=false
INFO [08-01|16:40:40.797] Imported new chain segment               blocks=1 txs=0 mgas=0.000 elapsed=5.391ms mgasps=0.000 number=1 hash=62d029..14f4c1 age=1mo4d23h dirty=7.07KiB
INFO [08-01|16:40:51.790] Finalized                                func=Finalize        block=2 epochSize=50000 duration="106.969µs" lastInEpoch=false
INFO [08-01|16:40:51.790] Imported new chain segment               blocks=1 txs=0 mgas=0.000 elapsed=4.913ms mgasps=0.000 number=2 hash=c2b741..35869d age=1mo4d23h dirty=10.79KiB
INFO [08-01|16:40:53.892] Finalized                                func=Finalize        block=3 epochSize=50000 duration="104.285µs" lastInEpoch=false
INFO [08-01|16:40:53.893] Finalized                                func=Finalize        block=4 epochSize=50000 duration="105.568µs" lastInEpoch=false
```
节点启动后会通过内置的引导节点连接到网络，并同步区块数据。

### 生成 ECDSA 签名

生成一个由 signer 账号签署的 ECDSA 签名，该 signer 账号签署了 validator 账号

```shell
./marker makeECDSASignatureFromSigner --target 0x73bc690093b9dd0400c91886184a60cc127b2c33 --signerPriv 040939e5...604b6f25

INFO [08-26|17:31:49.422] === makeECDSASignatureFromSigner === 
INFO [08-26|17:31:49.422] === signer  ===                          account=0x26654Eb0Bb935DCE4a34DAA3e14c67662a8Aa1f8
INFO [08-26|17:31:49.422] ECDSASignature                           result=0x59dff185...32f0d700
```


### 生成证明

使用 signer 私钥生成证明。

```shell
./marker generateSignerProof --validator 0x73bc690093b9dd0400c91886184a60cc127b2c33 --signerPriv 040939e5...604b6f25

INFO [08-26|17:32:23.950] generateBLSProof                         validator=0x01ccDcd1aE63a2C6B4c3493983dc6400C63729Ad signerPrivate=040939e5...604b6f25
INFO [08-26|17:32:23.955] === makeBLSProofOfPossessionFromSigner === 
INFO [08-26|17:32:23.958] generateBLSProof                         proof=0xf90149b8...0e56f0ab1
```

### 创建账号

```shell
./marker createAccount --rpcaddr https://rpc.maplabs.io --keystore ./account.json --name "validator"

INFO [07-08|14:54:28.097] Create account                           func=createAccount address=0x73bC690093b9dD0400c91886184A60cC127b2c33 name=validator
INFO [07-08|14:54:28.097] === create Account === 
INFO [07-08|14:54:28.102] TxInfo                                   func=sendContractTransaction TX data nonce =0  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =1,098,789
INFO [07-08|14:54:28.103] Please waiting                           func=getResult                txHash =0xa8d9dfb5b74720a940e904972784225d605ebb111fe92b87184ccb3a54ffd452
INFO [07-08|14:54:30.726] Transaction Success                      func=queryTx                 block Number=9
INFO [07-08|14:54:30.726] === setName name === 
INFO [07-08|14:54:30.731] TxInfo                                   func=sendContractTransaction TX data nonce =1  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =1,098,789
INFO [07-08|14:54:30.732] Please waiting                           func=getResult                txHash =0xf26f4f8b4a7f6ab44a70fe474162fe22b1bcd7694b4c1a42ed0b1a70d062d5ee
INFO [07-08|14:54:35.779] Transaction Success                      func=queryTx                 block Number=10
INFO [07-08|14:54:35.779] === setAccountDataEncryptionKey === 
INFO [07-08|14:54:35.784] TxInfo                                   func=sendContractTransaction TX data nonce =2  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =1,098,789
INFO [07-08|14:54:35.785] Please waiting                           func=getResult                txHash =0x430533536a6305b603c9abd6282a4fec359c7f42b8dbd78cf4d3f340ec9bf31b
INFO [07-08|14:54:40.224] Transaction Success                      func=queryTx                 block Number=11
```

### 使用签名授权

使用 signer 签署的 ECDSA 签名 (0x59dff185...32f0d700) 进行授权

signer 账号：0x98efa292822eb7b3045c491e8ae4e82b3b1ac005
signer 私钥：8df920b696ef3f5fdcf01624405ea8236b2b4907766ad61d42ce877df05f8bca

```shell
./marker authorizeValidatorSignerBySignature --rpcaddr http://127.0.0.1:7445 --keystore ./account.json --signer 0x26654eb0bb935dce4a34daa3e14c67662a8aa1f8 --signature 0x59dff185...32f0d700

INFO [07-08|14:55:00.015] authorizeValidatorSignerBySignature      signer=0x26654eb0bb935dce4a34daa3e14c67662a8aa1f8    signature=0x59dff185...32f0d700
INFO [07-08|14:55:00.032] Please waiting                           func=getResult                 txHash =0xb73a1376e661d523e44b87c37e2e03cc36534d3a550808245f263aaad358b0ad
INFO [07-08|14:55:05.078] Transaction Success                      func=queryTx                  block Number=16
```

### 锁定 MAPO

```shell
./marker lockedMAP --rpcaddr https://rpc.maplabs.io --keystore ./account.json --lockedNum 1000000

INFO [07-08|14:54:49.141] === Lock  gold === 
INFO [07-08|14:54:49.141] Lock  gold                               amount=1000000000000000000000000
INFO [07-08|14:54:49.148] TxInfo                                   func=sendContractTransaction TX data nonce =3  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =1,098,789
INFO [07-08|14:54:49.150] Please waiting                           func=getResult                txHash =0x698140b0ad8677706a4d10d3c5c72f15a8e143623be84a6ed514990fe3f5e5f3
INFO [07-08|14:54:50.765] Transaction Success                      func=queryTx                 block Number=13
```

### 使用证明注册 validator

通过使用 signer 的私钥生成的证明进行注册 validator。

```shell
./marker registerByProof --rpcaddr http://127.0.0.1:7445 --keystore ./account.json --proof 0xf90149b8...0e56f0ab1 --commission 150000

INFO [08-26|17:32:25.055] registerValidatorByProof                 commission=150,000
INFO [08-26|17:32:25.548] === getTotalVotesForValidator ===        admin=0x73bc690093b9dd0400c91886184a60cc127b2c33
INFO [08-26|17:32:25.974] === getTotalVotesForValidator ===        result=0
INFO [08-26|17:32:26.011] TxInfo                                   func=sendContractTransaction TX data nonce =7  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =1,098,789
INFO [08-26|17:32:26.119] Please waiting                           func=getResult                txHash =0xfb0487e7196df7489e90ab91217251da5fc7d07b2bc2d2f4ea67966a506a4cd6
INFO [08-26|17:32:27.241] Transaction Success                      func=queryTx                 block Number=194
```

### 验证注册

至此我们已经完成了 validator 的注册步骤，现在我们来验证一下是否成为了 validator。

```shell
./marker getTotalVotesForEligibleValidators --rpcaddr https://rpc.maplabs.io

INFO [07-08|15:10:03.301] Validator:                               addr=0xeA9efaA232A4567EaC21C8C096f8BfF84595A244 vote amount=1,000,000,000,000,000,000,000,000
INFO [07-08|15:10:03.301] Validator:                               addr=0x6ACdC02223100189d82A958d888F54fA27d60e8A vote amount=1,000,000,000,000,000,000,000,000
INFO [07-08|15:10:03.301] Validator:                               addr=0xA53516D49A72019692Ac69cB42641942597654f6 vote amount=1,000,000,000,000,000,000,000,000
INFO [07-08|15:10:03.301] Validator:                               addr=0x5d643Dfb9ae372ce4Fdbc80890156E2CD8290846 vote amount=1,000,000,000,000,000,000,000,000
INFO [07-08|15:10:03.301] Validator:                               addr=0x73bC690093b9dD0400c91886184A60cC127b2c33 vote amount=0
```
### 投票

投票数不能大于锁定票数。

有关投票和选举的更多信息，请点击以下链接查看：
[投票](/docs/base/mapo-relay-chain/marker/vote.md#vote)
[选举](/docs/base/mapo-relay-chain/protocol/election.md)

现在我们来给 validator 投票，投票的命令如下：

```shell
./marker vote --rpcaddr https://rpc.maplabs.io --keystore ./account.json --target 0x73bc690093b9dd0400c91886184a60cc127b2c33 --voteNum 1000000

INFO [07-08|15:11:13.693] === vote Validator ===                   admin=0x73bc690093b9dd0400c91886184a60cc127b2c33 voteTargetValidator=0x73bC690093b9dD0400c91886184A60cC127b2c33 vote MAP Num=1000000
INFO [07-08|15:11:13.709] TxInfo                                   func=sendContractTransaction TX data nonce =4  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =1,098,789
INFO [07-08|15:11:13.710] Please waiting                           func=getResult                txHash =0x8ec67797871ea313f7beea33900db8f680ddf2d01f35894c65e1212151729747
INFO [07-08|15:11:15.123] Transaction Success                      func=queryTx                 block Number=210
```

### 验证投票 

现在，让我们验证投票是否有效。

```shell
./marker getTotalVotesForEligibleValidators --rpcaddr https://rpc.maplabs.io

INFO [07-08|15:21:45.881] Validator:                               addr=0xeA9efaA232A4567EaC21C8C096f8BfF84595A244 vote amount=1,000,000,000,000,000,000,000,000
INFO [07-08|15:21:45.881] Validator:                               addr=0x6ACdC02223100189d82A958d888F54fA27d60e8A vote amount=1,000,000,000,000,000,000,000,000
INFO [07-08|15:21:45.881] Validator:                               addr=0xA53516D49A72019692Ac69cB42641942597654f6 vote amount=1,000,000,000,000,000,000,000,000
INFO [07-08|15:21:45.881] Validator:                               addr=0x5d643Dfb9ae372ce4Fdbc80890156E2CD8290846 vote amount=1,000,000,000,000,000,000,000,000
INFO [07-08|15:21:45.881] Validator:                               addr=0x73bC690093b9dD0400c91886184A60cC127b2c33 vote amount=1,000,000,000,000,000,000,000,000

```

从结果来看，我已经成功给自己投票了，但是还不够。我们需要在下一个 epoch 中 调用 RPC 来最终确定我们是否被选为可以参与区块生成的验证者，如下所示：

```shell
curl -X POST -H "Content-Type: application/json" -d '{"jsonrpc":"2.0","method":"istanbul_getValidators","params":[],"id":1}' https://rpc.maplabs.io

# Output
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": [
        "0x5d643dfb9ae372ce4fdbc80890156e2cd8290846",
        "0xa53516d49a72019692ac69cb42641942597654f6",
        "0x6acdc02223100189d82a958d888f54fa27d60e8a",
        "0xea9efaa232a4567eac21c8c096f8bff84595a244",
        "0x98efa292822eb7b3045c491e8ae4e82b3b1ac005" # our signer
    ]
}
```
