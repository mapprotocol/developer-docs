关于 validator 的注册、注销等介绍。

## register

注册一个新的 validator。通过这个命令，我们将把您的佣金，ecdsaPublicKey、blsPublicKey、blsG1PublicKey、BLSProof转移到管理合约中，
以管理和保护您的资产。你的 ecdsaPublicKey、blsPublicKey、BLSProof 将通过你指定的 keystore 获取。Validator 用于共识的 ECDSA
公钥应与验证者签名者匹配，长度为64字节。 验证者用于共识的 BLS公钥应通过所有权证明，长度为129字节。验证者用于共识的BLS G1公钥，
长度为129字节。BLS公钥的所有权证明由对账户地址的签名组成，长度为129字节。

参数说明:

- `rpcaddr`: RPC 服务的地址，可以是我们提供的的 [RPC 服务地址](/docs/base/mapo-relay-chain/public-service.md#网络信息)
  也可以是你自己的 RPC服务地址。
- `keystore`: keystore 文件的路径。
- `commission`: 佣金，validator 收取的奖励比例，然后将剩余部分分配给 voter，佣金参数相对于 1000000 设置的，其范围是 0 到 1000000
  如果你想将佣金比例设置问 15%, 那么你需要将该参数设置为 150000 (150000/1000000=15%)。这个属性是投票人投票时参考的对象之一。

```shell
./marker register
--rpcaddr http://127.0.0.1:7445
--keystore ./UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15
--commission 0.1

RESPONSE:
success
or
Failed
```

## quicklyRegister

快速注册一个新的 validator

如果您还没有创建账户或锁定 MAPO，您可以通过 quicklyRegister 命令快速注册，该命令集成了 createAccount、lockedMAP、register
三个命令。

请注意，您只能使用此命令一次。无论命令成功与否，此命令中包含了 createAccount lockedMAP 命令对应的操作，并不具备重复使用的特性。

参数说明:

- `rpcaddr`: RPC 服务的地址，可以是我们提供的的 [RPC 服务地址](/docs/base/mapo-relay-chain/public-service.md#网络信息)
  也可以是你自己的 RPC服务地址。
- `keystore`: keystore 文件的路径。
- `commission`: 佣金，validator 收取的奖励比例，然后将剩余部分分配给 voter，佣金参数相对于 1000000 设置的，其范围是 0 到
  1000000
  如果你想将佣金比例设置问 15%, 那么你需要将该参数设置为 150000 (150000/1000000=15%)。这个属性是投票人投票时参考的对象之一。
- `lockedNum`: 为了注册为 validator，您将锁定 MAPO 数量。
- `signerPriv`：signer 账号的私钥。

```shell
./marker quicklyRegister
--rpcaddr http://127.0.0.1:7445
--keystore ./UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15
--commission 100000
--signerPriv 842e1d8a93b4e46104da96676066ddb0973c63ec80a15746856c046dd4a1004c
--lockedNum 1000000

RESPONSE:
success
or
Failed
```

## deregister

此命令用于注销 validator，你必须是一个 validator 才能使用该命令。

合约设置了成为 validator 的最短时间（默认为60天）。在注销验证者之时，您必须超过这个时间。

为了防止在注销期间恶意占用资源，我们将您的注销请求置于待定状态，并在 epoch 的最后一个区块执行批量注销。

参数说明:

- `rpcaddr`: RPC 服务的地址，可以是我们提供的的 [RPC 服务地址](/docs/base/mapo-relay-chain/public-service.md#网络信息)
  也可以是你自己的 RPC服务地址。
- `keystore`: keystore 文件的路径。

```shell
./marker deregister
--rpcaddr http://127.0.0.1:7445
--keystore ./UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15


RESPONSE:
success
or
Failed
```

## revertRegister

如果您在某个 epoch 中注销您了您的的账户，您可以在同一 epoch 使用该命令恢复您的 validator 身份。

参数说明:

- `rpcaddr`: RPC 服务的地址，可以是我们提供的的 [RPC 服务地址](/docs/base/mapo-relay-chain/public-service.md#网络信息)
  也可以是你自己的 RPC服务地址。
- `keystore`: keystore 文件的路径。

```shell
./marker revertRegister
--rpcaddr http://127.0.0.1:7445
--keystore ./UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15


RESPONSE:
success
or
Failed
```

## authorizeValidatorSigner

在成为 validator 之前调用此方法。

如果您需要授权另一个账号来完成链上共识操作而不是使用 validator 账号，可以调用此方法进行授权。从而让另一个账号来完成链上共识操作。

参数说明:

- `rpcaddr`: RPC 服务的地址，可以是我们提供的的 [RPC 服务地址](/docs/base/mapo-relay-chain/public-service.md#网络信息)
  也可以是你自己的 RPC服务地址。
- `keystore`: keystore 文件的路径。
- `signerPriv`：signer 账号的私钥。

```shell
./marker authorizeValidatorSigner
--rpcaddr http://127.0.0.1:7445
--keystore ./UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15
--signerPriv   842e1d8a93b4e46104da96676066ddb0973c63ec80a15746856c046dd4a1004c   

RESPONSE:
INFO [05-30|13:41:55.131] === authorizeValidatorSigner === 
INFO [05-30|13:41:55.140] TxInfo                                   func=sendContractTransaction  TX data nonce =3  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =214
INFO [05-30|13:41:55.141] Please waiting                           func=getResult                 txHash =0x6ccc9758199c105c2b8eb9f9807b553baaa9062ec871a1fc47f3866a4a1e8d49
INFO [05-30|13:42:03.231] Transaction Success                      func=queryTx                  block Number=37

```

## makeECDSASignatureFromSigner

生成一个由 signer 账号签署的 ECDSA 签名，该 signer 账号签署了 validator 账号

参数说明:

- `signerPriv`：signer 账号的私钥。
- `target`：您要进行签名的 validator 账号的地址。

```shell
makeECDSASignatureFromSigner
--signerPriv
c59c8a05f70b5ea0a0f2a2bd9491686a8c4a55c0585db2c8c6ed7ccfa0ee2c7b
--target
0xac146d6629F8C3B8F2e830275B583C5402032472

RESPONSE:
INFO [05-30|13:41:55.131] === ECDSASignature === 
INFO [05-30|13:41:55.131] === signer ===                           account=0x05D0CFd882185dEB9b3E0eA7872Ad332acB9E31d
INFO [05-30|13:41:55.131] ECDSASignature                           result=0x6dcec34a67a3388b6fbf93ad48f88aa88c0ce46789de0f9e042acbe6e116c97f26d645652576a65602ed97f7ca16f890898b8e761de3edc34447edb2e41713ad01

```

## makeBLSProofOfPossessionFromSigner

生成一个 BLSProofOfPossession，由 signer 账号对 validator 账号进行 BLS 签名。

参数说明:

- `signerPriv`：signer 账号的私钥。
- `target`：您要进行签名的 validator 账号的地址。

```shell
makeBLSProofOfPossessionFromSigner
--signerPriv
c59c8a05f70b5ea0a0f2a2bd9491686a8c4a55c0585db2c8c6ed7ccfa0ee2c7b
--target
0xac146d6629F8C3B8F2e830275B583C5402032472

RESPONSE:
INFO [05-30|13:52:40.371] === makeBLSProofOfPossessionFromSigner === 
INFO [05-30|13:52:40.375] === pop ===                                result=0x1417ef1814518ade2af0e52ce7a11bcf834bba5ac42f91f3a1229c072721bb1b0c82513600690ebc0244572dd459d280abd6c14c0fc4837fa06335c88457a402

```

## signerToAccount

查询对目标 signer 账号进行授权的 validator 账号。

参数说明:

- `rpcaddr`: RPC 服务的地址，可以是我们提供的的 [RPC 服务地址](/docs/base/mapo-relay-chain/public-service.md#网络信息)
  也可以是你自己的 RPC服务地址。
- `keystore`: keystore 文件的路径。
- `target`：signer 账号的地址。

```shell
signerToAccount
--rpcaddr http://127.0.0.1:7445
--keystore ./UTC--2022-05-11T03-31-08.562982000Z--6621f2b6da2bed64b5ffbd6c5b2138547f44c8f9
--target "0x05D0CFd882185dEB9b3E0eA7872Ad332acB9E31d"

RESPONSE:
INFO [05-30|13:52:40.371] === signerToAccount === 
INFO [05-30|13:56:05.126] signerToAccount                          authorizingAccount=0xa88cF1842AC49A22435D6B4e4c1d8782e3C29EfE
```

## generateSignerProof

使用 signer 私钥生成证明。

参数说明:

- `validator`：validator 账号的地址。
- `signerPriv`：signer 账号的私钥。

```shell
./marker generateSignerProof 
--validator 0x73bc690093b9dd0400c91886184a60cc127b2c33 
--signerPriv 040939e5...604b6f25

RESPONSE:
INFO [08-26|17:32:23.950] generateBLSProof                         validator=0x01ccDcd1aE63a2C6B4c3493983dc6400C63729Ad signerPrivate=040939e5...604b6f25
INFO [08-26|17:32:23.955] === makeBLSProofOfPossessionFromSigner === 
INFO [08-26|17:32:23.958] generateBLSProof                         proof=0xf90149b8...0e56f0ab1
```

## authorizeValidatorSignerBySignature

在成为 validator 之前调用此方法。

如果您需要授权另一个账号来完成链上共识操作而不是使用 validator 账号，可以调用此方法进行授权。从而让另一个账号来完成链上共识操作。

参数说明:

- `rpcaddr`: RPC 服务的地址，可以是我们提供的的 [RPC 服务地址](/docs/base/mapo-relay-chain/public-service.md#网络信息)
  也可以是你自己的 RPC服务地址。
- `keystore`: keystore 文件的路径。
- `signer`: signer 账号的地址。
- `signature` 使用 [makeECDSASignatureFromSigner](#makeECDSASignatureFromSigner) 命令生成的 ECDSA 签名。

```shell                                                                                               
./marker authorizeValidatorSignerBySignature 
--rpcaddr http://127.0.0.1:7445 
--keystore ./account.json 
--signer 0x26654eb0bb935dce4a34daa3e14c67662a8aa1f8 
--signature 0x59dff185...32f0d700

RESPONSE:
INFO [07-08|14:55:00.015] authorizeValidatorSignerBySignature      signer=0x26654eb0bb935dce4a34daa3e14c67662a8aa1f8    signature=0x59dff185...32f0d700
INFO [07-08|14:55:00.032] Please waiting                           func=getResult                 txHash =0xb73a1376e661d523e44b87c37e2e03cc36534d3a550808245f263aaad358b0ad
INFO [07-08|14:55:05.078] Transaction Success                      func=queryTx                  block Number=16

```

## registerByProof

使用 `generateSignerProof` 生成的证明来注册 validator。

参数说明:

- `rpcaddr`: RPC 服务的地址，可以是我们提供的的 [RPC 服务地址](/docs/base/mapo-relay-chain/public-service.md#网络信息)
  也可以是你自己的 RPC服务地址。
- `keystore`: keystore 文件的路径。
- `proof`: 使用 [generateSignerProof](#generateSignerProof) 命令生成的证明。
- `commission`: 佣金，validator 收取的奖励比例，然后将剩余部分分配给 voter，佣金参数相对于 1000000 设置的，其范围是 0 到
  1000000
  如果你想将佣金比例设置问 15%, 那么你需要将该参数设置为 150000 (150000/1000000=15%)。这个属性是投票人投票时参考的对象之一。

```shell
./marker registerByProof 
--rpcaddr http://127.0.0.1:7445 
--keystore ./account.json -
--proof 0xf90149b8...0e56f0ab1 
--commission 150000

RESPONSE:
INFO [08-26|17:32:25.055] registerValidatorByProof                 commission=150,000
INFO [08-26|17:32:25.548] === getTotalVotesForValidator ===        admin=0x73bc690093b9dd0400c91886184a60cc127b2c33
INFO [08-26|17:32:25.974] === getTotalVotesForValidator ===        result=0
INFO [08-26|17:32:26.011] TxInfo                                   func=sendContractTransaction TX data nonce =7  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =1,098,789
INFO [08-26|17:32:26.119] Please waiting                           func=getResult                txHash =0xfb0487e7196df7489e90ab91217251da5fc7d07b2bc2d2f4ea67966a506a4cd6
INFO [08-26|17:32:27.241] Transaction Success                      func=queryTx 
```