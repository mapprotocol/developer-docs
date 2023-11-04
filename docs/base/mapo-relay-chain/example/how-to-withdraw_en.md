This document will provide a complete guide on how to perform redemption operations. There are two types of redemption
based on different roles: validator redemption and voter redemption.

## Validator withdraw

### Query locked MAPO amount

You can use the following command to query the amount of locked MAPO, which represents the amount of MAPO available for
redemption.

```shell
T-2:bin t$ ./marker getAccountTotalLockedGold --rpcaddr https://rpc.maplabs.io --target 0xa116617832F02cFFFb9aE8b78D8299E31A27Cc8f
INFO [10-13|14:52:08.383] === getAccountTotalLockedGold ===        admin=0x0000000000000000000000000000000000000000 target=0xa116617832F02cFFFb9aE8b78D8299E31A27Cc8f
INFO [10-13|14:52:10.410] result                                   lockedGold=230,038,174,196,116,506,009,036
```

### Unregister validator

**This step is not mandatory**, but if your locked balance is less than 10000000 after unlocking, please proceed with
the unregistration. The unregistration can only be performed after being registered as a validator for 60 days, and it
will take effect after the last block of the current epoch.

```shell
./marker deregister --rpcaddr http://127.0.0.1:7445 --keystore /Users/alex/data/atlas-1/keystore/UTC--2022-06-14T05-46-17.312327000Z--73bc690093b9dd0400c91886184a60cc127b2c33

INFO [08-02|16:52:40.688] === deregisterValidator === 
INFO [08-02|16:52:40.701] TxInfo                                   func=sendContractTransaction TX data nonce =10  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =22776
INFO [08-02|16:52:40.702] Please waiting                           func=getResult                txHash =0xb904cae42c8e9d5481b0023b367a35a5be37802c1a9f7a9ff55835cd72044def
INFO [08-02|16:52:41.107] Transaction Success                      func=queryTx                 block Number=1211
```

### Unlocking

This step is used to transition the status of MAPO from locked to pending redemption.

```shell
./marker unlockMap --rpcaddr http://127.0.0.1:7445 --keystore  /Users/alex/data/atlas-1/keystore/UTC--2022-06-14T05-46-17.312327000Z--73bc690093b9dd0400c91886184a60cc127b2c33 --lockedNum 200000

INFO [08-02|17:24:16.166] === unLock validator gold === 
INFO [08-02|17:24:16.166] unLock validator gold                    amount=200,000,000,000,000,000,000,000 admin=0x73bC690093b9dD0400c91886184A60cC127b2c33
INFO [08-02|17:24:16.180] TxInfo                                   func=sendContractTransaction TX data nonce =15  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =22776
INFO [08-02|17:24:16.181] Please waiting                           func=getResult                txHash =0x70b5c777daa507ca6b6e6f9132e757f8804c4147f0f8542f71a456ecf079bffa
INFO [08-02|17:24:21.229] Transaction Success                      func=queryTx                 block Number=1591
```

### Query Pending Withdrawals

```shell
./marker getPendingWithdrawals --rpcaddr http://127.0.0.1:7445 --target 0x73bc690093b9dd0400c91886184a60cc127b2c33

INFO [08-02|17:24:28.336] === getPendingWithdrawals ===            admin=0x0000000000000000000000000000000000000000 target=0x73bC690093b9dD0400c91886184A60cC127b2c33
INFO [08-02|17:24:28.343] result:                                  index=0 values=100,000,000,000,000,000,000,000 timestamps=1,660,727,111
INFO [08-02|17:24:28.343] result:                                  index=1 values=200,000,000,000,000,000,000,000 timestamps=1,660,728,211
```

From the output, we can see that there are two pending withdrawal amounts, indexed as 0 and 1. Next, we will withdraw
the funds with index 1.

### Withdraw

This step will convert the reward status from pending redemption to the account balance. However, this step can only be executed after a 15-day unlocking period.

```shell
./marker withdrawMap --rpcaddr http://127.0.0.1:7445 --keystore /Users/alex/data/atlas-1/keystore/UTC--2022-06-14T05-46-17.312327000Z--73bc690093b9dd0400c91886184a60cc127b2c33 --withdrawIndex 1

INFO [08-02|17:24:44.848] === withdraw validator gold ===          admin=0x73bC690093b9dD0400c91886184A60cC127b2c33
INFO [08-02|17:24:44.858] TxInfo                                   func=sendContractTransaction TX data nonce =16  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =22776
INFO [08-02|17:24:44.859] Please waiting                           func=getResult                txHash =0x1d592838b4044a05072692ebc9e8ef68b3f14ff6faa690283f4f363bc6642639
INFO [08-02|17:24:46.072] Transaction Success                      func=queryTx                 block Number=1596
```

### Query account balance

Now that the redeemed MAPO has been added to your account balance, let's verify it.

```shell
web3.fromWei(eth.getBalance("0x73bc690093b9dd0400c91886184a60cc127b2c33"), "ether")

200000.940633728411925694
```

## Voter withdraw

### Query voted validators

Query which validators the current account has voted for.

```shell
./marker getValidatorsVotedForByAccount --rpcaddr http://127.0.0.1:7445 --target 0x6d842e9c25c0c6246231296ca6ecf4bc8268949f

INFO [08-03|14:41:27.096] === getValidatorsVotedForByAccount ===   admin=0x0000000000000000000000000000000000000000
INFO [08-03|14:41:27.100] validator                                Address=0x73bC690093b9dD0400c91886184A60cC127b2c33
```

### Query active votes

Query the number of active votes from your account for a validator.

```shell
./marker getActiveVotesForValidatorByAccount --rpcaddr http://127.0.0.1:7445 --keystore /Users/alex/data/atlas-1/keystore/UTC--2022-06-17T03-50-52.931374000Z--6d842e9c25c0c6246231296ca6ecf4bc8268949f --target 0x73bc690093b9dd0400c91886184a60cc127b2c33

INFO [08-03|14:41:59.009] === getActiveVotesForValidatorByAccount === admin=0x6D842E9c25C0c6246231296ca6ECf4bC8268949F
INFO [08-03|14:41:59.011] ActiveVotes                              balance=100,648,411,651,178,302,866,465
```

### Revoke active vote

Revoke your active vote for a validator. This step will transition the status of MAPO from active to unvoted.

```shell
./marker revokeActive --rpcaddr http://127.0.0.1:7445 --keystore /Users/alex/data/atlas-1/keystore/UTC--2022-06-17T03-50-52.931374000Z--6d842e9c25c0c6246231296ca6ecf4bc8268949f --target 0x73bc690093b9dd0400c91886184a60cc127b2c33 --lockedNum 50000
INFO [08-03|14:43:01.909] === revokeActive ===                     admin=0x6D842E9c25C0c6246231296ca6ECf4bC8268949F
INFO [08-03|14:43:01.928] TxInfo                                   func=sendContractTransaction TX data nonce =5  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =22776
INFO [08-03|14:43:01.931] Please waiting                           func=getResult                txHash =0x8dd81d60fdddb8e23f8edaeb243477bce34ab8e835d594f1081b17d4d48c089d
INFO [08-03|14:43:10.818] Transaction Success                      func=queryTx                 block Number=438
```

### Query pending votes

Query the number of pending votes from your account for a validator.

```shell
./marker getPendingVotesForValidatorByAccount --rpcaddr http://127.0.0.1:7445 --keystore /Users/alex/data/atlas-1/keystore/UTC--2022-06-17T03-50-52.931374000Z--6d842e9c25c0c6246231296ca6ecf4bc8268949f

INFO [08-03|14:42:15.107] === getPendingVotesForValidatorByAccount === admin=0x6D842E9c25C0c6246231296ca6ECf4bC8268949F
INFO [08-03|14:42:15.109] ActiveVotes                              balance=10,000,000,000,000,000,000,000
```

### Revoke pending Votes

Revoke your pending votes. This step will transition the status of MAPO from pending to unvoted.

```shell
./marker revokePending --rpcaddr http://127.0.0.1:7445 --keystore /Users/alex/data/atlas-1/keystore/UTC--2022-06-17T03-50-52.931374000Z--6d842e9c25c0c6246231296ca6ecf4bc8268949f --target 0x73bc690093b9dd0400c91886184a60cc127b2c33 --lockedNum 10000
INFO [08-03|14:44:05.201] === revokePending ===                     admin=0x6D842E9c25C0c6246231296ca6ECf4bC8268949F
INFO [08-03|14:44:05.220] TxInfo                                   func=sendContractTransaction TX data nonce =6  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =22776
INFO [08-03|14:44:05.223] Please waiting                           func=getResult                txHash =0x8dd81d60fdddb8e23f8edaeb243477bce34ab8e835d594f1081b17d4d48c089d
INFO [08-03|14:44:15.723] Transaction Success                      func=queryTx                 block Number=438
```

### Query nonvoting MAPO amount

```shell
./marker getAccountNonvotingLockedGold --rpcaddr http://127.0.0.1:7445 --target 0x6d842e9c25c0c6246231296ca6ecf4bc8268949f
INFO [08-03|14:44:20.659] === getAccountNonvotingLockedGold ===    admin=0x0000000000000000000000000000000000000000 target=0x6D842E9c25C0c6246231296ca6ECf4bC8268949F
INFO [08-03|14:44:20.661] result                                   lockedGold=60,000,000,000,000,000,000,000
```

## Unlocking

This step is used to transition the status of MAPO from locked to pending redemption.

```shell
./marker unlockMap --rpcaddr http://127.0.0.1:7445 --keystore /Users/alex/data/atlas-1/keystore/UTC--2022-06-17T03-50-52.931374000Z--6d842e9c25c0c6246231296ca6ecf4bc8268949f --lockedNum 60000
INFO [08-03|14:45:33.629] === unLock validator gold === 
INFO [08-03|14:45:33.629] unLock validator gold                    amount=50,000,000,000,000,000,000,000 admin=0x6D842E9c25C0c6246231296ca6ECf4bC8268949F
INFO [08-03|14:45:33.641] TxInfo                                   func=sendContractTransaction TX data nonce =7  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =22776
INFO [08-03|14:45:33.642] Please waiting                           func=getResult                txHash =0xd23397b70eb3e13c90fa79c9c4e793569fb21a59bb80719a795864e6c5398b04
INFO [08-03|14:45:37.074] Transaction Success                      func=queryTx                 block Number=468
```

### Query pending withdrawals

```shell
./marker getPendingWithdrawals --rpcaddr http://127.0.0.1:7445 --target 0x6d842e9c25c0c6246231296ca6ecf4bc8268949f
INFO [08-03|14:45:57.066] === getPendingWithdrawals ===            admin=0x0000000000000000000000000000000000000000 target=0x6D842E9c25C0c6246231296ca6ECf4bC8268949F
INFO [08-03|14:45:57.069] result:                                  index=0 values=60,000,000,000,000,000,000,000 timestamps=1,660,805,137
```

### withdraw

This step will convert the reward status from pending redemption to the account balance. However, this step can only be executed after a 15-day unlocking period.

```shell
./marker withdrawMap --rpcaddr http://127.0.0.1:7445 --keystore /Users/alex/data/atlas-1/keystore/UTC--2022-06-17T03-50-52.931374000Z--6d842e9c25c0c6246231296ca6ecf4bc8268949f --withdrawIndex 0

INFO [08-03|14:46:21.582] === withdraw validator gold ===          admin=0x6D842E9c25C0c6246231296ca6ECf4bC8268949F
INFO [08-03|14:46:21.591] TxInfo                                   func=sendContractTransaction TX data nonce =8  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =22776
INFO [08-03|14:46:21.592] Please waiting                           func=getResult                txHash =0xdc8d99434cdb69ea22cd631b2768999126cf2c7ccc41cfb9fc8f2fabcf301e8c
INFO [08-03|14:46:25.229] Transaction Success                       func=queryTx                 Block Number=477
```

### Query account balance

You can query your account balance to verify if the withdraw MAPO has been added to your account.

```shell
> web3.fromWei(eth.getBalance("0x6d842e9c25c0c6246231296ca6ecf4bc8268949f"), "ether")

60000.880957764
```