# 搭建私有网络

## 前提条件

- 私钥和地址
- 构建 atlas 需要 git、Go（1.14 或更高版本）和 C 编译器
- 克隆代码仓库

```shell
git clone https://github.com/mapprotocol/atlas.git
```

## 构建

我们可以在一个主机上启动四个不同端口的节点，也可以在多个主机上启动四个节点。以下将使用在一个主机上启动四个不同端口的节点的方法来进行演示。

```shell
git checkout v1.1.5

make atlas
```

## 创建账号

Atlas 允许开发者在 Atlas 区块链上使用以太坊的账户。如果没有账户，可以使用下面的命令创建。

```shell
$ ./atlas --datadir ./node-1 account new
$ ./atlas --datadir ./node-2 account new
$ ./atlas --datadir ./node-3 account new
$ ./atlas --datadir ./node-4 account new

# Output
INFO [10-09|17:37:36.179] Maximum peer count                       ETH=100 LES=0 total=100
Your new account is locked with a password. Please give a password. Do not forget this password.
Password: 
Repeat password: 

Your new key was generated

Address:   0x...
PublicKey:   0x...
BLS Public key:   0x...
BLS G1 Public key:   0x...
BLSProofOfPossession:   0x...
Path of the secret key file: node-1/keystore/UTC--2023-10-09T09-37-38.019902000Z--...

- You can share your public address with anyone. Others need it to interact with you.
- You must NEVER share the secret key with anyone! The key controls access to your funds!
- You must BACKUP your key file! Without the key, it's impossible to access account funds!
- You must REMEMBER your password! Without the password, it's impossible to decrypt the key!
```

## 生成 genesis.json 文件

如何生成 genesis.json 文件，请参考 [这里](/docs/base/mapo-relay-chain/nodes/genesis-config.md#生成genesis.json文件)

我们现在使用上一步生成的四个账号的信息来生成 genesis.json 文件。将我们创建账户时输出的信息填入到 `markerConfig.json` 文件相应的键值中。AdminAddress
对应的值可以是我们上面创建的四个账户之一，也可以是其他账户。

```json
{
  "AdminAddress": "0x...",
  "Validators": [
    {
      "Address": "0x...",
      "SignerAddress": "0x...",
      "ECDSASignature": "0x...",
      "PublicKeyHex": "0x...",
      "BLSPubKey": "0x...",
      "BLSG1PubKey": "0x...",
      "BLSProofOfPossession": "0x..."
    },
    {
      "Address": "0x...",
      "SignerAddress": "0x...",
      "ECDSASignature": "0x...",
      "PublicKeyHex": "0x...",
      "BLSPubKey": "0x...",
      "BLSG1PubKey": "0x...",
      "BLSProofOfPossession": "0x..."
    },
    {
      "Address": "0x...",
      "SignerAddress": "0x...",
      "ECDSASignature": "0x...",
      "PublicKeyHex": "0x...",
      "BLSPubKey": "0x...",
      "BLSG1PubKey": "0x...",
      "BLSProofOfPossession": "0x..."
    },
    {
      "Address": "0x...",
      "SignerAddress": "0x...",
      "ECDSASignature": "0x...",
      "PublicKeyHex": "0x...",
      "BLSPubKey": "0x...",
      "BLSG1PubKey": "0x...",
      "BLSProofOfPossession": "0x..."
    },
  ]
}
```

然后使用我们上面的的 `markerConfig.json` 文件生成 genesis.json 文件，像下面这样：

```shell
marker genesis --buildpath /home/atlas-contracts/build/contracts --markercfg /home/atlas/cmd/marker/markerConfig.json

Output:
INFO [10-09|17:53:57.915] New deployment                           obj=deployment admin_address=0xB16561A66B6439944DAf0388b5E6a2D3D0a49e12
INFO [10-09|17:53:57.915] Running deploy step                      obj=deployment number=0
INFO [10-09|17:53:57.952] Running deploy step                      obj=deployment number=1
INFO [10-09|17:53:57.954] Start Deploy of Proxied Contract         obj=deployment contract=Registry proxyAddress=0x000000000000000000000000000000000000ce10 implAddress=0x000000000000000000000000000000000000CE11
INFO [10-09|17:53:57.954] Deploy Proxy                             obj=deployment contract=Registry
INFO [10-09|17:53:57.954] Deploy Implementation                    obj=deployment contract=Registry
INFO [10-09|17:53:57.954] Set proxy implementation                 obj=deployment contract=Registry
INFO [10-09|17:53:57.955] Initialize Contract                      obj=deployment contract=Registry
INFO [10-09|17:53:57.955] Add entry to registry                    obj=deployment name=Registry address=0x000000000000000000000000000000000000ce10
INFO [10-09|17:53:57.955] Running deploy step                      obj=deployment number=2
INFO [10-09|17:53:57.961] Start Deploy of Proxied Contract         obj=deployment contract=GoldToken proxyAddress=0x000000000000000000000000000000000000D003 implAddress=0x000000000000000000000000000000000000F003
INFO [10-09|17:53:57.961] Deploy Proxy                             obj=deployment contract=GoldToken
INFO [10-09|17:53:57.961] Deploy Implementation                    obj=deployment contract=GoldToken
INFO [10-09|17:53:57.961] Set proxy implementation                 obj=deployment contract=GoldToken
INFO [10-09|17:53:57.962] Initialize Contract                      obj=deployment contract=GoldToken
INFO [10-09|17:53:57.962] Add entry to registry                    obj=deployment name=GoldToken address=0x000000000000000000000000000000000000D003
INFO [10-09|17:53:57.962] Running deploy step                      obj=deployment number=3
......
```

等待命令执行完成后就可以在当前目录下找到 genesis.json 文件


## 初始化四个验证节点

使用在前一步骤中生成的创世文件初始化节点

```shell
./atlas --datadir ./node-1 init ./genesis.json

Output:
INFO [10-09|18:06:37.801] Maximum peer count                       ETH=100 LES=0 total=100
INFO [10-09|18:06:37.804] Set global gas cap                       cap=50,000,000
INFO [10-09|18:06:37.804] Allocated cache and file handles         database=/Users/t/go_project/mapprotocol/atlas/node-1/atlas/chaindata cache=16.00MiB handles=16
INFO [10-09|18:06:37.917] Writing custom genesis block 
INFO [10-09|18:06:37.924] Persisted trie from memory database      nodes=296 size=32.99KiB time="753.884µs" gcnodes=0 gcsize=0.00B gctime=0s livenodes=1 livesize=-738.00B
INFO [10-09|18:06:37.925] Successfully wrote genesis state         database=chaindata hash=0f40ec..668040
INFO [10-09|18:06:37.925] Allocated cache and file handles         database=/Users/t/go_project/mapprotocol/atlas/node-1/atlas/lightchaindata cache=16.00MiB handles=16
INFO [10-09|18:06:38.007] Writing custom genesis block 
INFO [10-09|18:06:38.011] Persisted trie from memory database      nodes=296 size=32.99KiB time="626.947µs" gcnodes=0 gcsize=0.00B gctime=0s livenodes=1 livesize=-738.00B
INFO [10-09|18:06:38.011] Successfully wrote genesis state         database=lightchaindata hash=0f40ec..668040
```

## 以下命令使用之前创建的四个账户启动了四个相应的节点

通过以下命令使用之前创建的四个账号启动四个相应的节点

```shell
./atlas --datadir ./node-1 --networkid 110112 --port 20201 --mine --miner.validator 0xB16561A66B6439944DAf0388b5E6a2D3D0a49e12 --unlock 0xB16561A66B6439944DAf0388b5E6a2D3D0a49e12 console
./atlas --datadir ./node-2 --networkid 110112 --port 20202 --mine --miner.validator 0x0e2699Be2B47Dfd7560c4771A32A50b64F81293d --unlock 0x0e2699Be2B47Dfd7560c4771A32A50b64F81293d console
./atlas --datadir ./node-3 --networkid 110112 --port 20203 --mine --miner.validator 0x3c0ef282c8c62a44eA2FE8928b6bd89a16fD8252 --unlock 0x3c0ef282c8c62a44eA2FE8928b6bd89a16fD8252 console
./atlas --datadir ./node-4 --networkid 110112 --port 20204 --mine --miner.validator 0x41E4A55Ef06c1961B3e143357e24cA7f92e4DD03 --unlock 0x41E4A55Ef06c1961B3e143357e24cA7f92e4DD03 console

```

在命令行输入上述命令，然后按回车键，您将看到以下提示：

```shell

......

INFO [10-09|18:16:28.639] Starting peer-to-peer node               instance=atlas/v1.1.5-stable/darwin-amd64/go1.20.7
INFO [10-09|18:16:28.759] New local node record                    seq=1,696,846,449,759 id=657ee01225705fca ip=127.0.0.1 udp=20201 tcp=20201
INFO [10-09|18:16:28.759] Started P2P networking                   self=enode://89b72450e02cf8a22dc66db717f4bab75b55961f2a474846f88615fd2be49bf406f3499842b39c5acd0d4ebc9fa72c29e27f1995a740d334c20b647e29a477b2@127.0.0.1:20201 maxdialed=33 maxinbound=67
INFO [10-09|18:16:28.762] IPC endpoint opened                      url=/Users/t/go_project/mapprotocol/atlas/node-1/atlas.ipc
Unlocking account 0x3BaB3BDe5ECC520134da32Bf7891578c108FC290 | Attempt 1/3
Password: 
```

此时，我们需要输入与账号对应的密码并按下回车键，恭喜，您已成功启动一个节点。然后我们以同样的方式启动剩余的节点。

如果你想启动一个 RPC 节点，可以使用以下方法。

```shell
./atlas --datadir ./node-rpc init ./genesis.json

./atlas --datadir ./node-rpc --networkid 110112 --port 20205 --http --http.addr "0.0.0.0" --http.port 7445 --http.api eth,web3,net,debug,txpool,header,istanbul --http.corsdomain "*" console
```

## 连接节点

在启动节点之前，请确保引导节点已经运行并且可以从外部访问（尝试 telnet <ip> <port> 以确保它确实可以访问）。
然后，通过 --bootnodes 将每个后续的 atlas 节点指向引导节点以进行对等节点发现。通过 --datadir 制定数据存储目录。

```shell
$ atlas --datadir=path/to/custom/data/folder --bootnodes=<bootnode-url>
```

查询自己的节点URL：

```shell
admin.nodeInfo.enode

Output:
enode://89b72450e02cf8a22dc66db717f4bab75b55961f2a474846f88615fd2be49bf406f3499842b39c5acd0d4ebc9fa72c29e27f1995a740d334c20b647e29a477b2@127.0.0.1:20201
```

在控制台使用一下命令连接节点

```shell
admin.addPeer("enode://89b72450e02cf8a22dc66db717f4bab75b55961f2a474846f88615fd2be49bf406f3499842b39c5acd0d4ebc9fa72c29e27f1995a740d334c20b647e29a477b2@127.0.0.1:20201")
admin.addPeer("enode://a4642d6eb2f1c69ee861f4146636df028a7eb328e233f620cc6838db474e94327bdcdc810d2f9c2fa30694764e71b4c7b5828f6e8df7a3f71f3eb781bb017a4e@127.0.0.1:20202")
admin.addPeer("enode://88c2fdd0189a33e3b8ee02a04a767c4792140c00c08de5d368b9aac578a0a36b5518aee5fcb695cd93c348237901a5c532f561170adc00903001e40ca3eff041@127.0.0.1:20203")
admin.addPeer("enode://88c2fdd0189a33e3b8ee02a04a767c4792140c00c08de5d368b9aac578a0a36b5518aee5fcb695cd93c348237901a5c532f561170adc00903001e40ca3eff041@127.0.0.1:20204")
```

## 检查节点状态

输入连接节点的命令后开始看到一些输出。几分钟后，你应该会看到像这样的行。这意味着你的节点已经连接了其他节点并将开始生成区块。

```shell
INFO [10-09|18:32:27.683] Looking for peers                        peercount=2 tried=5 static=4
INFO [10-09|18:32:37.582] Reset timer to resend RoundChange msg    address=0xB16561A66B6439944DAf0388b5E6a2D3D0a49e12 func=resetResendRoundChangeTi
mer cur_seq=23 cur_epoch=1 cur_round=0 des_round=5 state="Waiting for new round" address=0x2dC45799000ab08E60b7441c36fCC74060Ccbe11 timeout=17.5s
INFO [03-16|20:32:39.311] Looking for peers                        peercount=3 tried=0 static=4
```

你也可以通过在控制台中输入 `admin.peers` 来查看连接的节点。