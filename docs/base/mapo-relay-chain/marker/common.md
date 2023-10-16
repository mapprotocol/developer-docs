本篇文章将介绍创建账号、锁定 MAPO 及其各种查询接口等功能。

## createAccount

创建账号

参数说明:

- `rpcaddr`: RPC 服务的地址，可以是我们提供的的 [RPC 服务地址](/docs/base/mapo-relay-chain/public-service.md#endpoints)
  也可以是你自己的 RPC服务地址。
- `keystore`: keystore 文件的路径。
- `name`：账号名称

```shell
./marker createAccount
--rpcaddr http://127.0.0.1:7445
--keystore ./UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15
--name "validator"
```

## lockedMAP

锁定用于投票或注册 validator 的 MAPO。

参数说明:

- `rpcaddr`: RPC 服务的地址，可以是我们提供的的 [RPC 服务地址](/docs/base/mapo-relay-chain/public-service.md#endpoints)
  也可以是你自己的 RPC服务地址。
- `keystore`: keystore 文件的路径。
- `lockedNum`: 要锁定的 MAPO 的数量``

```shell
./marker lockedMAP
--rpcaddr http://127.0.0.1:7445
--keystore ./UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15
--lockedNum 1000000
```

## unlockMap

解锁 MAPO，解锁期过后可以赎回

参数说明:

- `rpcaddr`: RPC 服务的地址，可以是我们提供的的 [RPC 服务地址](/docs/base/mapo-relay-chain/public-service.md#endpoints)
  也可以是你自己的 RPC服务地址。
- `keystore`: keystore 文件的路径。
- `lockedNum`: 要解锁的 MAPO 的数量``

```shell
./marker unlockMap
--rpcaddr http://127.0.0.1:7445
--keystore ./UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15
--lockedNum 1000000
```

## relockMAP

重新锁定已解锁但未赎回的 MAPO

参数说明:

- `rpcaddr`: RPC 服务的地址，可以是我们提供的的 [RPC 服务地址](/docs/base/mapo-relay-chain/public-service.md#endpoints)
  也可以是你自己的 RPC服务地址。
- `keystore`: keystore 文件的路径。
- `lockedNum`: 锁定的 MAPO 的数量``
- `relockIndex`:
  待赎回的请求编号，通过 [getPendingWithdrawals](/docs/base/mapo-relay-chain/marker/common.md#getpendingwithdrawals) 命令查询

```shell
./marker relockMAP
--rpcaddr http://127.0.0.1:7445
--keystore ./UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15
--lockedNum 100000
--relockIndex 1
```

## withdrawMap

赎回已过解锁期的 MAPO。

参数说明:

- `rpcaddr`: RPC 服务的地址，可以是我们提供的的 [RPC 服务地址](/docs/base/mapo-relay-chain/public-service.md#endpoints)
  也可以是你自己的 RPC服务地址。
- `keystore`: keystore 文件的路径。
- `withdrawIndex`:
  待赎回的请求编号，通过 [getPendingWithdrawals](/docs/base/mapo-relay-chain/marker/common.md#getpendingwithdrawals) 命令查询


```shell
USAGE
./marker withdrawMap
--rpcaddr http://127.0.0.1:7445
--keystore ./UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15
--withdrawIndex 1
```

## getNumRegisteredValidators

获取已注册的 validator 的数量。

参数说明:

- `rpcaddr`: RPC 服务的地址，可以是我们提供的的 [RPC 服务地址](/docs/base/mapo-relay-chain/public-service.md#endpoints)
  也可以是你自己的 RPC服务地址。

```shell
./marker getNumRegisteredValidators --rpcaddr http://127.0.0.1:7445
```

## getTopValidators

返回 validator 集合中的前 N 个 validator 。

参数说明:

- `rpcaddr`: RPC 服务的地址，可以是我们提供的的 [RPC 服务地址](/docs/base/mapo-relay-chain/public-service.md#endpoints)
  也可以是你自己的 RPC服务地址。
- `topNum`: 指定要查询的前几个 validator。

```shell
./marker GetTopValidators
--rpcaddr http://127.0.0.1:7445
--topNum 6

RESPONSE:
INFO [03-14|17:04:48.606] === getTopValidators ===                 admin=0x0000000000000000000000000000000000000000
INFO [03-14|17:04:48.636] Validator:                               index=0 addr=0x1c0eDab88dbb72B119039c4d14b1663525b3aC15
INFO [03-14|17:04:48.636] Validator:                               index=1 addr=0x16FdBcAC4D4Cc24DCa47B9b80f58155a551ca2aF
INFO [03-14|17:04:48.636] Validator:                               index=2 addr=0x2dC45799000ab08E60b7441c36fCC74060Ccbe11
INFO [03-14|17:04:48.636] Validator:                               index=3 addr=0x6C5938B49bACDe73a8Db7C3A7DA208846898BFf5
```

## getTotalVotesForEligibleValidators

返回所有可投票的 validator 列表以及他们收到的票数。

参数说明:

- `rpcaddr`: RPC 服务的地址，可以是我们提供的的 [RPC 服务地址](/docs/base/mapo-relay-chain/public-service.md#endpoints)
  也可以是你自己的 RPC服务地址。

```shell
./marker getTotalVotesForEligibleValidators
--rpcaddr http://127.0.0.1:7445


RESPONSE:
INFO [03-14|17:06:13.020] === getTotalVotesForEligibleValidators === admin=0x0000000000000000000000000000000000000000
INFO [03-14|17:06:13.049] Validator:                               addr=0x1c0eDab88dbb72B119039c4d14b1663525b3aC15 vote amount=70,350,500,000,000,000,000,000,000
INFO [03-14|17:06:13.049] Validator:                               addr=0x16FdBcAC4D4Cc24DCa47B9b80f58155a551ca2aF vote amount=70,322,500,000,000,000,000,000,000
INFO [03-14|17:06:13.049] Validator:                               addr=0x2dC45799000ab08E60b7441c36fCC74060Ccbe11 vote amount=70,322,500,000,000,000,000,000,000
INFO [03-14|17:06:13.049] Validator:                               addr=0x6C5938B49bACDe73a8Db7C3A7DA208846898BFf5 vote amount=70,322,500,000,000,000,000,000,000
INFO [03-14|17:06:13.049] Validator:                               addr=0x81f02Fd21657DF80783755874a92c996749777Bf vote amount=32,968,000,000,000,000,000,000,000

```

## getTotalVotes

获取所有 validator 收到的总票数。

参数说明:

- `rpcaddr`: RPC 服务的地址，可以是我们提供的的 [RPC 服务地址](/docs/base/mapo-relay-chain/public-service.md#endpoints)
  也可以是你自己的 RPC服务地址。

```shell
./marker getTotalVotes
--rpcaddr http://127.0.0.1:7445

RESPONSE:
INFO [03-14|17:07:24.458] === getAccountLockedGoldRequirement ===  admin=0x0000000000000000000000000000000000000000
INFO [03-14|17:07:24.487] result                                   getTotalVotes=315,096,000,000,000,000,000,000,000
```

## getValidatorEligibility

查询 validator 是否有获得投票的资格。

参数说明:

- `rpcaddr`: RPC 服务的地址，可以是我们提供的的 [RPC 服务地址](/docs/base/mapo-relay-chain/public-service.md#endpoints)
  也可以是你自己的 RPC服务地址。
- `target`: 要查询的 validator 地址。

```shell
./marker getValidatorEligibility
--rpcaddr http://127.0.0.1:7445
--target 0x1c0edab88dbb72b119039c4d14b1663525b3ac15

RESPONSE:
INFO [03-14|17:10:27.990] === getValidatorEligibility ===          admin=0x0000000000000000000000000000000000000000
INFO [03-14|17:10:28.018] === result ===                           bool=true
```

## getValidator

获取指定 validator 的信息。

参数说明:

- `rpcaddr`: RPC 服务的地址，可以是我们提供的的 [RPC 服务地址](/docs/base/mapo-relay-chain/public-service.md#endpoints)
  也可以是你自己的 RPC服务地址。
- `target`: 要查询的 validator 地址。

```shell
./marker getValidator
--rpcaddr http://127.0.0.1:7445
--target 0x1c0edab88dbb72b119039c4d14b1663525b3ac15

RESPONSE:
INFO [03-14|17:12:22.303] === getValidator ===                     admin=0x0000000000000000000000000000000000000000
INFO [03-14|17:12:22.332]                                          ecdsaPublicKey=0x2b5e2a3beacf839d1ec74fd00f4388d4b813eac26b26ab4859003473b286650a
INFO [03-14|17:12:22.332]                                          BlsPublicKey=0x1fd39f1fbcad8e3188442ea31dee662389599751f8e73b99215cefc2e0003f81
INFO [03-14|17:12:22.332]                                          Score=1
INFO [03-14|17:12:22.332]                                          Signer=0x1c0eDab88dbb72B119039c4d14b1663525b3aC15
INFO [03-14|17:12:22.332]                                          Commission=0.1
INFO [03-14|17:12:22.333]                                          NextCommission=0
INFO [03-14|17:12:22.333]                                          NextCommissionBlock=0
INFO [03-14|17:12:22.333]                                          SlashMultiplier=1
INFO [03-14|17:12:22.333]                                          LastSlashed=0
```

## getValidatorRewardInfo

返回 validator 上一个 epoch 的奖励信息。

参数说明:

- `rpcaddr`: RPC 服务的地址，可以是我们提供的的 [RPC 服务地址](/docs/base/mapo-relay-chain/public-service.md#endpoints)
  也可以是你自己的 RPC服务地址。

```shell
./marker getValidatorRewardInfo --rpcaddr http://127.0.0.1:7445

RESPONSE:
INFO [03-14|17:13:42.872] === getReward ===                        cur_epoch=389 epochSize=20 queryBlockNumber=7760 validatorContractAddress=0x000000000000000000000000000000000000D012 admin=0x0000000000000000000000000000000000000000
INFO [03-14|17:13:42.874]                                          validator=0x1c0eDab88dbb72B119039c4d14b1663525b3aC15 reward=19,999,999,999,999,999,999,999
INFO [03-14|17:13:42.874]                                          validator=0x16FdBcAC4D4Cc24DCa47B9b80f58155a551ca2aF reward=19,999,999,999,999,999,999,999
INFO [03-14|17:13:42.874]                                          validator=0x2dC45799000ab08E60b7441c36fCC74060Ccbe11 reward=19,999,999,999,999,999,999,999
INFO [03-14|17:13:42.874]                                          validator=0x6C5938B49bACDe73a8Db7C3A7DA208846898BFf5 reward=19,999,999,999,999,999,999,999
INFO [03-14|17:13:42.874]                                          validator=0x81f02Fd21657DF80783755874a92c996749777Bf reward=9,999,999,999,999,999,999,999
INFO [03-14|17:13:42.874] === END === 
```

## getPendingVotesForValidatorByAccount

获取指定账号对指定 validator 的待定投票数。

参数说明:

- `rpcaddr`: RPC 服务的地址，可以是我们提供的的 [RPC 服务地址](/docs/base/mapo-relay-chain/public-service.md#endpoints)
  也可以是你自己的 RPC服务地址。
- `keystoreAddress`: 账号地址。
- `target`: 要查询的 validator 地址。

```shell
./marker getPendingVotesForValidatorByAccount 
--rpcaddr https://rpc.maplabs.io
--keystoreAddress 0x1c0edab88dbb72b119039c4d14b1663525b3ac15 
--target 0x51e297bae83b653405ec44cb8dd28792e5510083 


INFO [10-10|23:14:22.329] === getPendingVotesForValidatorByAccount === admin=0x1c0edab88dbb72b119039c4d14b1663525b3ac15
INFO [10-10|23:14:24.944] PendingVotes                             balance=0
```

## getActiveVotesForValidatorByAccount

获取指定账号对指定 validator 的激活投票数。

参数说明:

- `rpcaddr`: RPC 服务的地址，可以是我们提供的的 [RPC 服务地址](/docs/base/mapo-relay-chain/public-service.md#endpoints)
  也可以是你自己的 RPC服务地址。
- `keystoreAddress`: 账号地址。
- `target`: 要查询的 validator 地址。

```shell
./marker getActiveVotesForValidatorByAccount 
--rpcaddr https://rpc.maplabs.io 
--keystoreAddress 0x1c0edab88dbb72b119039c4d14b1663525b3ac15 
--target 0x51e297bae83b653405ec44cb8dd28792e5510083 

RESPONSE:
INFO [10-10|23:14:58.531] === getActiveVotesForValidatorByAccount === admin=0x1c0edab88dbb72b119039c4d14b1663525b3ac15
INFO [10-10|23:15:01.851] ActiveVotes                              balance=100,000,568,936,610,167,111,833
```

## getPendingInfoForValidator

获取指定账号对指定 validator 进行的待处理投票以及待处理的 epoch。

参数说明:

- `rpcaddr`: RPC 服务的地址，可以是我们提供的的 [RPC 服务地址](/docs/base/mapo-relay-chain/public-service.md#endpoints)
  也可以是你自己的 RPC服务地址。
- `keystoreAddress`: 账号地址。
- `target`: 要查询的 validator 地址。

```shell
./marker getPendingInfoForValidator 
--rpcaddr https://rpc.maplabs.io 
--keystoreAddress 0x1c0edab88dbb72b119039c4d14b1663525b3ac15 
--target 0x51e297bae83b653405ec44cb8dd28792e5510083

RESPONSE:
INFO [03-14|17:20:49.046] === getPendingInfoForValidator ===       admin=0x0000000000000000000000000000000000000000
INFO [03-14|17:20:49.074] getPendingInfoForValidator               Value=0 Epoch=0
```

## getValidatorsVotedForByAccount

获取指定账号已投票的 validator 列表。

参数说明:

- `rpcaddr`: RPC 服务的地址，可以是我们提供的的 [RPC 服务地址](/docs/base/mapo-relay-chain/public-service.md#endpoints)
  也可以是你自己的 RPC服务地址。
- `target`: 要查询的账号地址。

```shell
./marker getValidatorsVotedForByAccount 
--rpcaddr https://rpc.maplabs.io 
--target 0x51e297bae83b653405ec44cb8dd28792e5510083

RESPONSE:
INFO [10-10|23:32:20.130] === getValidatorsVotedForByAccount ===   admin=0x0000000000000000000000000000000000000000
INFO [10-10|23:32:23.092] validator                                Address=0x51E297bAE83b653405ec44Cb8DD28792E5510083
```

## getAccountTotalLockedGold

获取账号锁定的 MAPO 的总量。

参数说明:

- `rpcaddr`: RPC 服务的地址，可以是我们提供的的 [RPC 服务地址](/docs/base/mapo-relay-chain/public-service.md#endpoints)
  也可以是你自己的 RPC服务地址。
- `target`: 要查询的账号地址。

```shell
./marker getAccountTotalLockedGold \
--rpcaddr https://rpc.maplabs.io \
--target 0x51e297bae83b653405ec44cb8dd28792e5510083

RESPONSE:
INFO [10-10|23:38:02.825] === getAccountTotalLockedGold ===        admin=0x0000000000000000000000000000000000000000 target=0x51E297bAE83b653405ec44Cb8DD28792E5510083
INFO [10-10|23:38:05.419] result                                   lockedGold=1,152,952,667,878,467,847,105,123
```

## getAccountNonvotingLockedGold

获取账号非投票锁定的 MAPO 的总量。

参数说明:

- `rpcaddr`: RPC 服务的地址，可以是我们提供的的 [RPC 服务地址](/docs/base/mapo-relay-chain/public-service.md#endpoints)
  也可以是你自己的 RPC服务地址。
- `target`:  要查询的账号地址。

```shell
./marker getAccountNonvotingLockedGold \
--rpcaddr https://rpc.maplabs.io \
--target 0x51e297bae83b653405ec44cb8dd28792e5510083

RESPONSE:
INFO [10-10|23:40:16.854] === getAccountNonvotingLockedGold ===    admin=0x0000000000000000000000000000000000000000 target=0x51E297bAE83b653405ec44Cb8DD28792E5510083
INFO [10-10|23:40:19.401] result                                   lockedGold=0
```

## getPendingWithdrawals

获取账号的待赎回的 MAPO 数量。

参数说明:

- `rpcaddr`: RPC 服务的地址，可以是我们提供的的 [RPC 服务地址](/docs/base/mapo-relay-chain/public-service.md#endpoints)
  也可以是你自己的 RPC服务地址。
- `target`:  要查询的账号地址。

```shell
./marker getPendingWithdrawals
--rpcaddr http://127.0.0.1:7445
--target 0x1c0edab88dbb72b119039c4d14b1663525b3ac15

RESPONSE:
INFO [03-14|17:33:18.696] === getPendingWithdrawals ===            admin=0x1c0eDab88dbb72B119039c4d14b1663525b3aC15 target=0x1c0eDab88dbb72B119039c4d14b1663525b3aC15
INFO [03-14|17:33:18.724] nil 
```

## transfer

将 MAPO 从一个账号转移到另一个账号。

参数说明:

- `rpcaddr`: RPC 服务的地址，可以是我们提供的的 [RPC 服务地址](/docs/base/mapo-relay-chain/public-service.md#endpoints)
  也可以是你自己的 RPC服务地址。
- `keystore`: keystore 文件的路径。
- `target`: 接受者的账号地址。
- `amount`: 要转移的 MAPO 数量。

```shell
./marker transfer \
--rpcaddr http://127.0.0.1:7445 \
--keystore /Users/alex/data/keystore/UTC--2022-05-31T03-33-25.405082000Z--3b778bb4f460956e313ba92484eb84603a86a625 \
--target 0x7cc3e34c2075d96ef69bf6445a234f6c5e244073 \
--amount 10

RESPONSE:
INFO [08-30|10:56:11.048] Tx Info                                  func=sendContractTransaction from=0x3B778BB4F460956E313Ba92484Eb84603A86a625 to=0x7cC3e34C2075D96ef69bF6445a234F6C5E244073 value=10 nonce =1  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =212
INFO [08-30|10:56:11.144] Please waiting                           func=getResult                txHash =0x55beb566d735e7b46e61d85f23e52b0958777ba4bbd2332244b3a2e5eb22e137
INFO [08-30|10:56:12.234] Please waiting, Transaction is in pending status func=getResult
INFO [08-30|10:56:13.328] Please waiting, Transaction is in pending status func=getResult
INFO [08-30|10:56:15.748] Transaction Success                      func=getResult               number=668,236
INFO [08-30|10:56:15.748] transfer success                         from =0x3B778BB4F460956E313Ba92484Eb84603A86a625 to=0x7cC3e34C2075D96ef69bF6445a234F6C5E244073 amount=10
```

## getAccountMetadataURL

获取账号元数据 URL。

参数说明:

- `rpcaddr`: RPC 服务的地址，可以是我们提供的的 [RPC 服务地址](/docs/base/mapo-relay-chain/public-service.md#endpoints)
  也可以是你自己的 RPC服务地址。
- `target`:  要查询的账号地址。

```shell
./marker getAccountMetadataURL \
--rpcaddr https://rpc.maplabs.io \
--target 0x82965be3c2784edca8ad4472c80ffbe44404da49

RESPONSE:
INFO [10-10|23:50:02.275] get account metadata url                 address=0x82965bE3c2784eDcA8ad4472c80FfBe44404Da49 url=
```

## setAccountMetadataURL

设置账号元数据 URL。

参数说明:

- `rpcaddr`: RPC 服务的地址，可以是我们提供的的 [RPC 服务地址](/docs/base/mapo-relay-chain/public-service.md#endpoints)
  也可以是你自己的 RPC服务地址。
- `keystore`: keystore 文件的路径。
- `target`: 账号地址。

```shell
./marker setAccountMetadataURL \
--rpcaddr http://127.0.0.1:7445 \
--keystore /Users/alex/data/keystore/UTC--2022-08-26T10-59-01.086763000Z--ef021f15d188ad28625517a8d73cd20ce743a32d  \
--url https://www.metadata.com

RESPONSE:
INFO [08-30|11:09:53.016] set account metadata url                 address=0xeF021f15D188ad28625517A8D73CD20cE743a32D url=https://www.metadata.com
INFO [08-30|11:09:54.678] Tx Info                                  func=sendContractTransaction from=0xeF021f15D188ad28625517A8D73CD20cE743a32D to=0x000000000000000000000000000000000000d010 value=<nil> nonce =5  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =20212
INFO [08-30|11:09:55.092] Please waiting                           func=getResult                txHash =0xde15a1daee5e74c183f759c00cf51ba09c31c76c01362a25e2020b1c04fa9f7d
INFO [08-30|11:09:56.508] Please waiting, Transaction is in pending status func=getResult
INFO [08-30|11:09:57.926] Please waiting, Transaction is in pending status func=getResult
INFO [08-30|11:09:58.350] Transaction Success                      func=getResult               number=61898
```

## getAccountName

获取账号名称。

参数说明:

- `rpcaddr`: RPC 服务的地址，可以是我们提供的的 [RPC 服务地址](/docs/base/mapo-relay-chain/public-service.md#endpoints)
  也可以是你自己的 RPC服务地址。
- `target`: 账号地址。

```shell
./marker getAccountName \
--rpcaddr https://rpc.maplabs.io \
--target 0x5d643dfb9ae372ce4fdbc80890156e2cd8290846

RESPONSE:
INFO [10-10|23:52:46.084] get name                                 address=0x5d643Dfb9ae372ce4Fdbc80890156E2CD8290846 name="MAP Protocol#1"
```

## setAccountName

设置账号名称。

参数说明:

- `rpcaddr`: RPC 服务的地址，可以是我们提供的的 [RPC 服务地址](/docs/base/mapo-relay-chain/public-service.md#endpoints)
  也可以是你自己的 RPC服务地址。
- `keystore`: keystore 文件的路径。
- `name`: 要设置的账号名称。

```shell
./marker setAccountName \
--rpcaddr http://127.0.0.1:7445 \
--keystore ./UTC--2022-08-26T10-59-01.086763000Z--ef021f15d188ad28625517a8d73cd20ce743a32d \
--name "so cool validator"

RESPONSE:
INFO [08-31|15:16:07.006] set name                                 address=0xeF021f15D188ad28625517A8D73CD20cE743a32D name="so cool validator"
INFO [08-31|15:16:09.039] Tx Info                                  func=sendContractTransaction from=0xeF021f15D188ad28625517A8D73CD20cE743a32D to=0x000000000000000000000000000000000000d010 value=<nil> nonce =10  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =20212
INFO [08-31|15:16:09.448] Please waiting                           func=getResult                txHash =0x046ee71f161c06d34c4eb13ed9c536e909da152d9ca4036ccb240ae1ab23cb7f
INFO [08-31|15:16:10.983] Please waiting, Transaction is in pending status func=getResult
INFO [08-31|15:16:12.517] Please waiting, Transaction is in pending status func=getResult
INFO [08-31|15:16:14.048] Please waiting, Transaction is in pending status func=getResult
INFO [08-31|15:16:17.024] Transaction Success                      func=getResult               number=82133
```

## setNextCommissionUpdate

将 validator 佣金更新请求加入到队列。如果之前有存在的更新请求，那么它将被覆盖。

参数说明:

- `rpcaddr`: RPC 服务的地址，可以是我们提供的的 [RPC 服务地址](/docs/base/mapo-relay-chain/public-service.md#endpoints)
  也可以是你自己的 RPC服务地址。
- `keystore`: keystore 文件的路径。
- `commission`: 佣金，validator 收取的奖励比例，然后将剩余部分分配给 voter，佣金参数相对于 1000000 设置的，其范围是 0 到 1000000
  如果你想将佣金比例设置问 15%, 那么你需要将该参数设置为 150000 (150000/1000000=15%)。这个属性是投票人投票时参考的对象之一。

```shell
./marker setNextCommissionUpdate \
--rpcaddr http://127.0.0.1:7445 \
--keystore ./UTC--2022-08-26T10-59-01.086763000Z--ef021f15d188ad28625517a8d73cd20ce743a32d \
--commission 300000

RESPONSE:
INFO [09-01|14:17:15.746] === setNextCommissionUpdate ===          commission=300,000
INFO [09-01|14:17:17.644] Tx Info                                  func=sendContractTransaction from=0xeF021f15D188ad28625517A8D73CD20cE743a32D to=0x000000000000000000000000000000000000D012 value=<nil> nonce =14  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =20212
INFO [09-01|14:17:18.062] Please waiting                           func=getResult                txHash =0xcb72b1a5055b290712a5f8e72a4bc60028b144ebea92c12637315891874ab8bf
INFO [09-01|14:17:20.911] Transaction Success                      func=getResult               number=98706
```

## updateCommission

根据之前队列中的更新请求更新佣金，该操作只能在执行完 [setNextCommissionUpdate](#setNextCommissionUpdate) 后的 2000
个区块后执行。

参数说明:

- `rpcaddr`: RPC 服务的地址，可以是我们提供的的 [RPC 服务地址](/docs/base/mapo-relay-chain/public-service.md#endpoints)
  也可以是你自己的 RPC服务地址。
- `keystore`: keystore 文件的路径。

```shell
./marker updateCommission \
--rpcaddr http://127.0.0.1:7445 \
--keystore ./UTC--2022-08-26T10-59-01.086763000Z--ef021f15d188ad28625517a8d73cd20ce743a32d

RESPONSE:
INFO [09-01|14:18:15.648] === updateCommission === 
INFO [09-01|14:18:17.303] Tx Info                                  func=sendContractTransaction from=0xeF021f15D188ad28625517A8D73CD20cE743a32D to=0x000000000000000000000000000000000000D012 value=<nil> nonce =15  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =20212
INFO [09-01|14:18:17.717] Please waiting                           func=getResult                txHash =0x4f252120cc61e27d72d47e6f6a9dd732bb9aa319937b94fdb602c6c93feb4c83
INFO [09-01|14:18:20.536] Transaction Success                      func=getResult               number=98718
```