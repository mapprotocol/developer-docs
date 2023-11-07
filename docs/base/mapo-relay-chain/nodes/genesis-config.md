marker genesis 是 marker 开发者工具提供的一个功能，用于轻松运行 Atlas 区块链测试网络和与测试网络相关的任务。

它相对于之前的解决方案的主要优势在于，它能够创建一个 genesis.json ，其中所有核心合约已经部署在其中

关于如何使用 marker 工具请参考 [这里](/docs/base/mapo-relay-chain/marker/overview.md#使用)。

## 生成 genesis.json 文件

```shell
git clone github.com/mapprotocol/atlas
cd atlas/marker/config
vim markerConfig.json
```

首先，你需要像这样配置 markerConfig.json 设置账号的相关信息。

```shell
{
 "AdminAddress": "0xB16561A66B6439944DAf0388b5E6a2D3D0a49e12",
 "Validators": [
  {
   "Address": "0xB16561A66B6439944DAf0388b5E6a2D3D0a49e12",
   "SignerAddress": "0x1c0eDab88dbb72B119039c4d14b1663525b3aC15",
   "ECDSASignature": "0x2ac44a912dbf3205f0500401cf02db33240fa03198dd4c3b202dfe7be3dbe1ed7cbfbe9326d50223faf2971e3ecefed027df1dda6727e9c45b55b377384dacf201",
   "PublicKeyHex": "0x0491c373a1504d67e0c6c98276fc6043544fd09a623473b6936a107943baf666612b5e2a3beacf839d1ec74fd00f4388d4b813eac26b26ab4859003473b286650a",
   "BLSPubKey": "0x136ef6be87de9c925869387782afb4cf19496999c2684709daeb3af8d0b59d800bbe05870789f0f9b3cadababa69f5a00a38bbcba71d99c4c35d671442232c4d3017fd6b99e8356a3e4e985bdfc60bbcb8d939c87976a1ff677d7c42989b379a0b4c0f168a544c892bd2b3ec480e3d6c58c7dddb8d83677ebee2e87ab3660b80",
   "BLSG1PubKey": "0x14d44a97d2fc3ea62b6dcf2bd857079bd261993152f11aef5dd001db68b20d2d1ba45f117b6530a7aec45d7d90fd4e15d2a62f62b706eaa115aa801caeee294b",
   "BLSProofOfPossession": "0x15933821ab5c324c95ba46ad99349e4c0d068a290fabd90f59eee9e69597ce012e4df0614f48696ff22c52a0667ee1daf8ca581d920c39ba726f0f3c69cae66e"
  },
  {
   "Address": "0xdC757c8e3b8800d977a34f802131FAA870d264c4",
   "SignerAddress": "0x16FdBcAC4D4Cc24DCa47B9b80f58155a551ca2aF",
   "ECDSASignature": "0x9b4f29d54a31b052fc9fcfdc968912a59b308df021f32b3c923438c0d65ff2cc47b8d01bf5968d70a8c82875a1852c313540f89467a3fe833902a0c0aff5a27a01",
   "PublicKeyHex": "0x04e7678fb997c00d5998f79413d73ebde98865cd0d7fa82e2ab6d0920a72204d8c49c14f873ec9ee0e0b38651001acc9a4c1a0a63de6c6589b896f21f6a6bb6837",
   "BLSPubKey": "0x0a2e37ecad6e69bfec9fec2b345d0f8441a0f63acf8b45c0131a78e5d777d52e0a39404ca85f2c08752c1d4ff8df05c82c7880779d61fe3fabcd4fd682463c0515b1f0217561a6a72bd381da19e34c5560c6eccb08ff83d7d3f4ac6da7f5d1ed15a2780f782c1fa571fa65b99694af559b9df168b1d8745ac3bbc7d3fe550b94",
   "BLSG1PubKey": "0x15b7bcf0accf839170a5d4621282edcf14f4a438f8e53abcead5f0528cb91cb1135fd4e82ede1493ab1209af122e1dc186c885cc96d2413cbc09a58163b91eb9",
   "BLSProofOfPossession": "0x2cfa8f2daa103964dfa380bfc30e695b94ef62832e1e641d9febf4ab78b128392f2e33612a66cc75e35a6326b8a6aa0be26fd9567d4aeff16e9883a671648b5d"
  },
  {
   "Address": "0x257Cc34FB139A2db4Da96496Be03358d89e52d95",
   "SignerAddress": "0x2dC45799000ab08E60b7441c36fCC74060Ccbe11",
   "ECDSASignature": "0x303ea4fdbd348c32655e7a460802be169f4bc4ca1a7030af941de4a57069dfb5253c0c087e06e258f63e50176bd5b54bf462514d8116ec3394bdc875c5678f7500",
   "PublicKeyHex": "0x04ec7664543f2dae218176a072ca7bfc16632438793077c06cf05975cc1302ee60c27f29e2cc3b64ffbaa69d2939e937f99a7bf93d7c5fa59bffbcd769e4f234e8",
   "BLSPubKey": "0x086fac850f3a9f36e8a5107eab0ba79044043dc2cc6b897cbbd0d4bf805570ff270a98f28e2d2e70b7b2ecc41a4a13e453178354997aa2038852c5945f0564bb02cdf57642881a1b40417fe3620429fc087f8dee6a68e5d7193d3243c38a1f3827d0f4cb616722a1fa78a283a17589d7688a769ade77e9d6417c6e2a9adf59c3",
   "BLSG1PubKey": "0x2fd433e93187f6b3d15664ec48073bd73d57c801c4a8bfc1e0e3abd3deefc45619d45ac7ad54df7dda5b8afd6f882c9d9f879dbc6d587f1da5da1751baac729f",
   "BLSProofOfPossession": "0x1ee63371c158365acde92ee1a53ca1dcb54efb52d1c077735dcd46237c7938b62720e4903dd9b825f3bed819422791352a9b7fe1a39e1183d33c0ca892cff1f4"
  },
  {
   "Address": "0xac146d6629F8C3B8F2e830275B583C5402032472",
   "SignerAddress": "0x6C5938B49bACDe73a8Db7C3A7DA208846898BFf5",
   "ECDSASignature": "0x29388d40ee2ee4468fca04b624c77208b7b00c56c532fac53314cf39e3e8feb80c6bd2b7d25a6e20e9da0adaeec4b49c13b673e6ca2d088370e0b6189fb216f000",
   "PublicKeyHex": "0x04ef2af91ba2fc2b04bc47c7d59d6d07a0dea2a62c5b537d4a83a387bee44245317de753c4e45858708c0d31473c6595ac9dddbcf7ac02a13df4af1a188e2c9c24",
   "BLSPubKey": "0x03fea7bc386ea24aaa19c563a4f26f38cbc2ce172ba2310587405f4f05777fb911a4c3553b7b6529ea02a9da3ae2df6f70c3409105b39e1930d6a6ae8344fc221f5dfb2e73cc8ce434d1af33d95366796bdec26ca7cfcc0a03867fabf471884206db6b9e175a131995bd0c70b93a6f2eec96d831ad0c42d13d334f780d578834",
   "BLSG1PubKey": "0x1b037f39d9f8e74b608a898249cc3d156ff1f0051026388366b85a84aac43bb4068275cd909e16b29f1b3bc97e91ec0a8b95a11b8a574cbc2c9ea142d26c8a49",
   "BLSProofOfPossession": "0x1417ef1814518ade2af0e52ce7a11bcf834bba5ac42f91f3a1229c072721bb1b0c82513600690ebc0244572dd459d280abd6c14c0fc4837fa06335c88457a402"
  }
 ]
}
```

## 参数说明

`Address`：账号地址，将作为 validator 账号注册到 validator 集合中。
`SignerAddress`：签名者地址，它是由账号地址授权的，用于替代 validator 与 Atlas 网络上的其他节点达成共识。
`ECDSASignature`：签名者地址对账号地址的 ECDSA 签名结果。
`PublicKeyHex`：签名者地址的公钥。
`BLSPubKey`：签名者地址的 BLS 公钥。
`BLSG1PubKey`：签名者地址的 BLS G1 公钥。
`BLSProofOfPossession`：签名者地址账户地址的BLS签名数据

关于 PublicKeyHex、BLSPubKey、BLSG1PubKey
如何获取请参考 [这里](/docs/base/mapo-relay-chain/make-private-network.md#创建账号)
关于 ECDSASignature 相关信息请参考 [这里](/docs/base/mapo-relay-chain/marker/validator.md#makeecdsasignaturefromsigner)
关于 BLSProofOfPossession 相关信息请参考 [这里](/docs/base/mapo-relay-chain/marker/validator.md#makeblsproofofpossessionfromsigner)

其次，您需要编译您的 atlas-contracts 项目，我们需要关于 atlas-contracts 的 bytecode 来生成 genesis.json 文件。
关于这部分信息请参考 [这里](/docs/base/mapo-relay-chain/genesis-contract/deploy.md#编译合约)

然后执行以下操作：

```shell
USAGE
  $ ./marker genesis

OPTIONS
  --buildpath:  buildpath is the path to truffle compile output folder.
  
  --newenv:   the genesis.json will be generated under this folder 

  --markercfg:  this your markerConfig.json path default to github.com/mapprotocol/atlas/marker/config/markerConfig.json
                                                     
EXAMPLES:

marker genesis --buildpath /home/atlas-contracts/build/contracts --markercfg /home/atlas/marker/config/markerConfig.json

```

上面的命令会在当前目录下生成一个 genesis.json 文件，你可以使用此 genesis.json 初始化 Atlas validator 节点。 像下面这样：

```shell
./atlas --datadir ./node-1 init ./genesis.json
```

另一种方法是将 genesis.json 中的 alloc
信息复制到 [core/chain/genesis_alloc_mainnet.go](https://github.com/mapprotocol/atlas/blob/8862631953900d1c5dbbdbb803812b9823678d74/core/chain/genesis_alloc_mainnet.go#L25)
文件中，覆盖 mainnetAllocJSON 。然后使用 `make atlas` 重新编译代码。

在这里我们更推荐第一种方式，因为你可以通过修改 genesis.json 文件来定制你想要的参数，比如你可以修改 chainId。并且这种方式更不容易出错。