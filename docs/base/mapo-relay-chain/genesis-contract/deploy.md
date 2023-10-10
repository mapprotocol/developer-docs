## 使用 Truffle 在 Atlas 上部署创世合约

### Truffle 简介

Truffle 是一个世界级的开发环境、测试框架和资产管道，用于使用以太坊虚拟机（EVM）的区块链。通过创建一个 Truffle 项目并编辑一些配置设置，
您可以轻松地在 Atlas 链上部署您的项目。

要在 Atlas 中继链上使用 Truffle 进行部署，您需要准备好本地环境。如果您不想使用本地环境进行部署，可以使用 Remix 或 Replit
进行部署。

如果你是 Truffle 的新手，请完成快速入门教程，以更加熟悉这个工具

### 项目设置

设置项目目录

打开终端窗口，创建一个项目目录并进入该目录。

### 初始化 Truffle

初始化 Truffle 会为您的 Truffle 项目创建一个脚手架

```shell
truffle init
```

### 配置部署设置

默认的truffle.config.js文件包含了部署到以太坊网络所需的连接，导入了HDWalletProvider，并连接到您的.env文件中的助记词。
要部署到 Atlas 网络，您需要更新此配置文件，将其指向不同的 Atlas 网络，并添加一些特定于 Atlas 最佳实践的细节。

### 更新truffle-config.js文件

在文本编辑器中打开truffle-config.js，并通过以下示例配置其内容：

```js
  networks: {
    atlas: {
        host: "https://rpc.maplabs.io",
            port
    :
        7445,
            network_id
    :
        "22776"
    }
}
```

### 编译合约

```shell
truffle compile
```

### 部署合约

使用以下命令将部署到您指定的 atlas 网络。

```shell
truffle deploy --network atlas
```

### 部署与 Validator 相关的合约

首先你需要编译 atlas-contracts 项目，我们需要关于 atlas-contracts 的 bytecode 来生成 genesis.json 文件。

1. 在任意文件夹中下载 atlas-contracts 项目，使用以下命令 `git clone https://github.com/mapprotocol/atlas-contracts.git`
2. 假设您已经安装了 node ，然后切换到项目文件，初始化项目并使用以下命令 `npm install`

3. 使用 `npm install truffle` 下载 truffle 并使用 truffle 编译项目，truffle 的编译命令是 `truffle compile` 保姆哦完成后在您的
   atlas-contracts 项目中将生成一个名为 build 的文件。我们将使用此文件来指定相应的参数。