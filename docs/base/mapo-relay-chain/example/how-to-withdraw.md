本篇文档将完整的介绍如何进行赎回操作。根据角色的不同赎回分为两种，一种是 validator 赎回，另一种是 voter 赎回。

## Validator 赎回

### 查询锁定的 MAPO 数量。

你可以调通过执行下面的命令查询锁定的 MAPO 数量，也是你可以进行赎回的 MAPO 数量。

```shell
T-2:bin t$ ./marker getAccountTotalLockedGold --rpcaddr https://rpc.maplabs.io --target 0xa116617832F02cFFFb9aE8b78D8299E31A27Cc8f
INFO [10-13|14:52:08.383] === getAccountTotalLockedGold ===        admin=0x0000000000000000000000000000000000000000 target=0xa116617832F02cFFFb9aE8b78D8299E31A27Cc8f
INFO [10-13|14:52:10.410] result                                   lockedGold=230,038,174,196,116,506,009,036
```

### 注销 validator

**此步骤不是必须的**，但如果解锁后您的锁定余额小于10000000，请先注销。注销操作只能在注册为验证人后 60 天才能进行，注销需要等待当前
epoch 的最后一个区块才能生效。

```shell
./marker deregister --rpcaddr http://127.0.0.1:7445 --keystore /Users/alex/data/atlas-1/keystore/UTC--2022-06-14T05-46-17.312327000Z--73bc690093b9dd0400c91886184a60cc127b2c33

INFO [08-02|16:52:40.688] === deregisterValidator === 
INFO [08-02|16:52:40.701] TxInfo                                   func=sendContractTransaction TX data nonce =10  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =22776
INFO [08-02|16:52:40.702] Please waiting                           func=getResult                txHash =0xb904cae42c8e9d5481b0023b367a35a5be37802c1a9f7a9ff55835cd72044def
INFO [08-02|16:52:41.107] Transaction Success                      func=queryTx                 block Number=1211
```

### 解锁

此步骤用于将 MAPO 的状态从锁定状态转换为待赎回状态。

```shell
./marker unlockMap --rpcaddr http://127.0.0.1:7445 --keystore  /Users/alex/data/atlas-1/keystore/UTC--2022-06-14T05-46-17.312327000Z--73bc690093b9dd0400c91886184a60cc127b2c33 --lockedNum 200000

INFO [08-02|17:24:16.166] === unLock validator gold === 
INFO [08-02|17:24:16.166] unLock validator gold                    amount=200,000,000,000,000,000,000,000 admin=0x73bC690093b9dD0400c91886184A60cC127b2c33
INFO [08-02|17:24:16.180] TxInfo                                   func=sendContractTransaction TX data nonce =15  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =22776
INFO [08-02|17:24:16.181] Please waiting                           func=getResult                txHash =0x70b5c777daa507ca6b6e6f9132e757f8804c4147f0f8542f71a456ecf079bffa
INFO [08-02|17:24:21.229] Transaction Success                      func=queryTx                 block Number=1591
```

### 查询待赎回状态的资金

```shell
./marker getPendingWithdrawals --rpcaddr http://127.0.0.1:7445 --target 0x73bc690093b9dd0400c91886184a60cc127b2c33

INFO [08-02|17:24:28.336] === getPendingWithdrawals ===            admin=0x0000000000000000000000000000000000000000 target=0x73bC690093b9dD0400c91886184A60cC127b2c33
INFO [08-02|17:24:28.343] result:                                  index=0 values=100,000,000,000,000,000,000,000 timestamps=1,660,727,111
INFO [08-02|17:24:28.343] result:                                  index=1 values=200,000,000,000,000,000,000,000 timestamps=1,660,728,211
```

查看输出，我们可以看到现在有两笔待赎回的资金，编号为 0 和 1。接下来我们将提取编号为 1 的资金。

### 赎回

此步骤会将奖励状态从待赎回状态兑换到账户余额，但此步骤需要解锁 15 天后才能执行。

```shell
./marker withdrawMap --rpcaddr http://127.0.0.1:7445 --keystore /Users/alex/data/atlas-1/keystore/UTC--2022-06-14T05-46-17.312327000Z--73bc690093b9dd0400c91886184a60cc127b2c33 --withdrawIndex 1

INFO [08-02|17:24:44.848] === withdraw validator gold ===          admin=0x73bC690093b9dD0400c91886184A60cC127b2c33
INFO [08-02|17:24:44.858] TxInfo                                   func=sendContractTransaction TX data nonce =16  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =22776
INFO [08-02|17:24:44.859] Please waiting                           func=getResult                txHash =0x1d592838b4044a05072692ebc9e8ef68b3f14ff6faa690283f4f363bc6642639
INFO [08-02|17:24:46.072] Transaction Success                      func=queryTx                 block Number=1596
```

### 查询账户余额

现在兑换的 MAPO 已经在账户余额中了，我们来验证一下。

```shell
web3.fromWei(eth.getBalance("0x73bc690093b9dd0400c91886184a60cc127b2c33"), "ether")

200000.940633728411925694
```

## voter 赎回

### 查询账户已投票的 validator。

查询当前账号对哪些 validator 进行了投票。

```shell
./marker getValidatorsVotedForByAccount --rpcaddr http://127.0.0.1:7445 --target 0x6d842e9c25c0c6246231296ca6ecf4bc8268949f

INFO [08-03|14:41:27.096] === getValidatorsVotedForByAccount ===   admin=0x0000000000000000000000000000000000000000
INFO [08-03|14:41:27.100] validator                                Address=0x73bC690093b9dD0400c91886184A60cC127b2c33
```

### 查询活跃投票数量

查询您的账号对 validator 的活跃投票数量。

```shell
./marker getActiveVotesForValidatorByAccount --rpcaddr http://127.0.0.1:7445 --keystore /Users/alex/data/atlas-1/keystore/UTC--2022-06-17T03-50-52.931374000Z--6d842e9c25c0c6246231296ca6ecf4bc8268949f --target 0x73bc690093b9dd0400c91886184a60cc127b2c33

INFO [08-03|14:41:59.009] === getActiveVotesForValidatorByAccount === admin=0x6D842E9c25C0c6246231296ca6ECf4bC8268949F
INFO [08-03|14:41:59.011] ActiveVotes                              balance=100,648,411,651,178,302,866,465
```

### 撤销活跃投票

撤销您对 validator 的活跃投票。此步骤会将 MAPO 的状态从活跃状态转换到未投票状态。

```shell
./marker revokeActive --rpcaddr http://127.0.0.1:7445 --keystore /Users/alex/data/atlas-1/keystore/UTC--2022-06-17T03-50-52.931374000Z--6d842e9c25c0c6246231296ca6ecf4bc8268949f --target 0x73bc690093b9dd0400c91886184a60cc127b2c33 --lockedNum 50000
INFO [08-03|14:43:01.909] === revokeActive ===                     admin=0x6D842E9c25C0c6246231296ca6ECf4bC8268949F
INFO [08-03|14:43:01.928] TxInfo                                   func=sendContractTransaction TX data nonce =5  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =22776
INFO [08-03|14:43:01.931] Please waiting                           func=getResult                txHash =0x8dd81d60fdddb8e23f8edaeb243477bce34ab8e835d594f1081b17d4d48c089d
INFO [08-03|14:43:10.818] Transaction Success                      func=queryTx                 block Number=438
```

### 查询非活跃投票数量

查询您的账号对 validator 的非活跃投票数量。

```shell
./marker getPendingVotesForValidatorByAccount --rpcaddr http://127.0.0.1:7445 --keystore /Users/alex/data/atlas-1/keystore/UTC--2022-06-17T03-50-52.931374000Z--6d842e9c25c0c6246231296ca6ecf4bc8268949f

INFO [08-03|14:42:15.107] === getPendingVotesForValidatorByAccount === admin=0x6D842E9c25C0c6246231296ca6ECf4bC8268949F
INFO [08-03|14:42:15.109] ActiveVotes                              balance=10,000,000,000,000,000,000,000
```

### 撤销非活跃投票

撤销您的非活跃投票。此步骤会将 MAPO 的状态从非活跃状态转换到未投票状态。

```shell
./marker revokePending --rpcaddr http://127.0.0.1:7445 --keystore /Users/alex/data/atlas-1/keystore/UTC--2022-06-17T03-50-52.931374000Z--6d842e9c25c0c6246231296ca6ecf4bc8268949f --target 0x73bc690093b9dd0400c91886184a60cc127b2c33 --lockedNum 10000
INFO [08-03|14:44:05.201] === revokePending ===                     admin=0x6D842E9c25C0c6246231296ca6ECf4bC8268949F
INFO [08-03|14:44:05.220] TxInfo                                   func=sendContractTransaction TX data nonce =6  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =22776
INFO [08-03|14:44:05.223] Please waiting                           func=getResult                txHash =0x8dd81d60fdddb8e23f8edaeb243477bce34ab8e835d594f1081b17d4d48c089d
INFO [08-03|14:44:15.723] Transaction Success                      func=queryTx                 block Number=438
```

### 查询未投票状态的 MAPO 数量

```shell
./marker getAccountNonvotingLockedGold --rpcaddr http://127.0.0.1:7445 --target 0x6d842e9c25c0c6246231296ca6ecf4bc8268949f
INFO [08-03|14:44:20.659] === getAccountNonvotingLockedGold ===    admin=0x0000000000000000000000000000000000000000 target=0x6D842E9c25C0c6246231296ca6ECf4bC8268949F
INFO [08-03|14:44:20.661] result                                   lockedGold=60,000,000,000,000,000,000,000
```

## 解锁

此步骤用于将 MAPO 的状态从锁定状态转换为待赎回状态。

```shell
./marker unlockMap --rpcaddr http://127.0.0.1:7445 --keystore /Users/alex/data/atlas-1/keystore/UTC--2022-06-17T03-50-52.931374000Z--6d842e9c25c0c6246231296ca6ecf4bc8268949f --lockedNum 60000
INFO [08-03|14:45:33.629] === unLock validator gold === 
INFO [08-03|14:45:33.629] unLock validator gold                    amount=50,000,000,000,000,000,000,000 admin=0x6D842E9c25C0c6246231296ca6ECf4bC8268949F
INFO [08-03|14:45:33.641] TxInfo                                   func=sendContractTransaction TX data nonce =7  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =22776
INFO [08-03|14:45:33.642] Please waiting                           func=getResult                txHash =0xd23397b70eb3e13c90fa79c9c4e793569fb21a59bb80719a795864e6c5398b04
INFO [08-03|14:45:37.074] Transaction Success                      func=queryTx                 block Number=468
```

### 查询待赎回状态的资金

```shell
./marker getPendingWithdrawals --rpcaddr http://127.0.0.1:7445 --target 0x6d842e9c25c0c6246231296ca6ecf4bc8268949f
INFO [08-03|14:45:57.066] === getPendingWithdrawals ===            admin=0x0000000000000000000000000000000000000000 target=0x6D842E9c25C0c6246231296ca6ECf4bC8268949F
INFO [08-03|14:45:57.069] result:                                  index=0 values=60,000,000,000,000,000,000,000 timestamps=1,660,805,137
```

### 赎回

此步骤会将奖励状态从待赎回状态兑换到账户余额，但此步骤需要解锁 15 天后才能执行。

```shell
./marker withdrawMap --rpcaddr http://127.0.0.1:7445 --keystore /Users/alex/data/atlas-1/keystore/UTC--2022-06-17T03-50-52.931374000Z--6d842e9c25c0c6246231296ca6ecf4bc8268949f --withdrawIndex 0

INFO [08-03|14:46:21.582] === withdraw validator gold ===          admin=0x6D842E9c25C0c6246231296ca6ECf4bC8268949F
INFO [08-03|14:46:21.591] TxInfo                                   func=sendContractTransaction TX data nonce =8  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =22776
INFO [08-03|14:46:21.592] Please waiting                           func=getResult                txHash =0xdc8d99434cdb69ea22cd631b2768999126cf2c7ccc41cfb9fc8f2fabcf301e8c
INFO [08-03|14:46:25.229] Transaction Success                       func=queryTx                 Block Number=477
```

### 查询账户余额

现在兑换的 MAPO 已经在账户余额中了，我们来验证一下。

```shell
> web3.fromWei(eth.getBalance("0x6d842e9c25c0c6246231296ca6ecf4bc8268949f"), "ether")

60000.880957764
```