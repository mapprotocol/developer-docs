# 投票

与投票操作相关命令的介绍

## vote

对您指定的 validator 进行投票。

您必须提前将足够的 MAPO [锁定](/docs/base/mapo-relay-chain/marker/common.md#lockedMAP)在 lockedGold 合同中，并将您的信息注册到合同中。

该操作会在 LockedGold 合约中，减少您先前注册的账号对应的总票数和未投票数并在 Election 合约中增加 validator 的总票数和待处理票数。

参数说明:

- `rpcaddr`: RPC 服务的地址，可以是我们提供的的 [RPC 服务地址](/docs/base/mapo-relay-chain/public-service.md#endpoints)
  也可以是你自己的 RPC服务地址。
- `keystore`: keystore 文件的路径。
- `target`：您要进行投票的 validator 的地址。
- `voteNum`：您要投票的 MAPO 数量。

```shell
./marker vote
--rpcaddr http://127.0.0.1:7445
--keystore ./UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15
--target "0x81f02fd21657df80783755874a92c996749777bf"
--voteNum 10000
```

## quicklyVote

如果您尚未创建账号和锁定 MAPO，您可以通过 quicklyVote 命令快速投票，该命令集成了 createAccount、 lockedMAP 和 vote 命令
请注意，您只能使用此命令一次。无论命令成功与否，此命令中包含了 createAccount、lockedMAP 和 vote 命令对应的操作，并不具备重复使用的特性。

参数说明:

- `rpcaddr`: RPC 服务的地址，可以是我们提供的的 [RPC 服务地址](/docs/base/mapo-relay-chain/public-service.md#endpoints)
  也可以是你自己的 RPC服务地址。
- `keystore`: keystore 文件的路径。
- `target`：您要进行投票的 validator 的地址。
- `voteNum`：您要投票的 MAPO 数量。
- `lockedNum`：您要锁定的 MAPO 数量。

```shell
./marker vote
--rpcaddr http://127.0.0.1:7445
--keystore ./UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15
--target "0x81f02fd21657df80783755874a92c996749777bf"
--voteNum 10000
--lockedNum 10000
```

## activate

激活待定选票以开始获得奖励。

作为 voter 获得奖励，需要在产生待定选票的 epoch 结束后的某个时间点激活它们。 这意味着将 account 的待定投票转换为有效投票。

参数说明:

- `rpcaddr`: RPC 服务的地址，可以是我们提供的的 [RPC 服务地址](/docs/base/mapo-relay-chain/public-service.md#endpoints)
  也可以是你自己的 RPC服务地址。
- `keystore`: keystore 文件的路径。
- `target`：您要进行激活投票的 validator 的地址。

```shell
./marker activate
--rpcaddr http://127.0.0.1:7445
--keystore ./UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15
--target "0x81f02fd21657df80783755874a92c996749777bf"
```

## revokePending

撤销对 validator 的待定投票。

这个命令将把投票状态的 MAPO 转换成非投票 MAPO ， 并在 LockedGold 合约中增加与您先前注册的账号对应的投票总数和非投票数。
在Election 合约中，减少验证者的总票数和待处理票数。

参数说明:

- `rpcaddr`: RPC 服务的地址，可以是我们提供的的 [RPC 服务地址](/docs/base/mapo-relay-chain/public-service.md#endpoints)
  也可以是你自己的 RPC服务地址。
- `keystore`: keystore 文件的路径。
- `target`：您要进行撤销待定投票的 validator 的地址。
- `lockNum`: 您想要从 validator 的待决投票中撤回的 MAPO 数量。

```shell
./marker revokePending
--rpcaddr http://127.0.0.1:7445
--keystore ./UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15
--target "0x81f02fd21657df80783755874a92c996749777bf"
--lockedNum 10000
```

## revokeActive

撤销对 validator 的活跃投票。

这个命令将把投票 MAPO 转变为非投票 MAPO 。 在 LockedGold 合约中增加与您先前注册的帐户对应的投票总数和非投票数。
在 Election 合约中，减少对应 validator 的总票数和活跃票数。

参数说明:

参数说明:

- `rpcaddr`: RPC 服务的地址，可以是我们提供的的 [RPC 服务地址](/docs/base/mapo-relay-chain/public-service.md#endpoints)
  也可以是你自己的 RPC服务地址。
- `keystore`: keystore 文件的路径。
- `target`：您要进行撤销待定投票的 validator 的地址。
- `lockNum`: 您想要从 validator 的活跃投票中撤回的 MAPO 数量。

```shell
./marker revokeActive
--rpcaddr http://127.0.0.1:7445
--keystore ./UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15
--target "0x81f02fd21657df80783755874a92c996749777bf"
--lockedNum 10000
```
