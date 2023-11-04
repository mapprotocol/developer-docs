## 什么是 marker

marker 是 atlas 提供的一个简易命令行工具，有了它您可以简单方便的完成一些操作。它允许您使用命令行方式而不用额外的编写脚本与
atlas 协议和智能合约进行交互。主要包含一些常见功能包括: 注册 validator, 帮助用户参与选举、链上治理、为 validator 投票等功能。

## 构建 marker

```shell
git clone https://github.com/mapprotocol/atlas.git
cd atlas
make marker
```

构建完成后您可以运行 “./build/bin/marker” 来启动 marker，或者切换到 ./build/bin 路径并运行 “./marker” 来启动 marker。

## 使用

marker 的大多数子命令在使用时需要连接到一个正在运行的 atlas RPC 节点，所以您需要先启动一个 atlas RPC 节点。如何启动 RPC
节点请[这里](/docs/base/mapo-relay-chain/nodes/rpc-nodes.md#运行-rpc-节点)
。或者您可以直接使用我们提供的[公共 RPC 节点](/docs/base/mapo-relay-chain/public-service.md#网络信息)。

还有一个需要验证身份的子命令需要使用到 keystore 文件，因此您在使用这些命令时需要提前准备好 keystore 文件，
您可以使用以下方法生成一个 keystore 文件:

1. [构建 atlas](/docs/base/mapo-relay-chain/nodes/run-a-node.md#克隆代码仓库并构建)
2. 使用 atlas 客户端生成 keystore 文件:

```shell
USAGE
 ./atlas account new --keystore "keystore path"
 
EXAMPLES:
 ./atlas account new --keystore ./datadir/keystore
 
RESPONSE:
Your new account is locked with a password. Please give a password. Do not forget this password.
Password:
Repeat password:

Your new key was generated

Public address of the key:   0x929510A8b54D3a8d7943e2Cdb5BA1888F7Ab7C4a
Path of the secret key file: ./datadir/keystore/UTC--2022-03-15T02-11-43.837807000Z--929510a8b54d3a8d7943e2cdb5ba1888f7ab7c4a
The keystore has been stored in the directory specified by --keystore.
```

如果您已经有一个账号，可以进入到 atlas 控制台进行转换，将你的账号私钥转换成 keystore 文件。

```shell
USAGE
./atlas --dataDir "./data" console
web3.personal.importRawKey("your private key","your password")
EXAMPLES:
> web3.personal.importRawKey("eaff...db280","password")
"0xd2f9e7716cc88944e5ed9f675649532c80d765f8"
The keystore has been stored in the directory specified by --dataDir.
```

执行完成后再当前目录的 data 目录下会生产一个 keystore 文件。
