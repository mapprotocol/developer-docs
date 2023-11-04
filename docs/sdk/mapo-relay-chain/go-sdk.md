`atlasclient` 分叉自 `ethclient`。 它继承了 `ethclient` 的大部分功能并加入了特定 Atlas 网络的功能。

`atlasclient` 是一种用于与 Atlas 网络进行交互的客户端。它提供了一种便捷的方式连接到 Atlas 网络，发送交易，与智能合约进行交互，并检索区块链数据。

`atlasclient` 的功能包括：

1. 连接 atlas 网络：使用 atlasclient，您可以连接到特定的以太坊网络，如主网或测试网。
2. 检索区块链数据：您可以从 Atlas 区块链中获取有关区块、交易和账户余额等信息。
3. 发送交易：您可以创建和发送交易来执行以太坊网络上的各种操作，如转账以太币或与智能合约进行交互。
4. 与智能合约进行交互：您可以使用 atlasclient 部署智能合约、调用合约函数和检索合约状态。

`atlasclient` 继承自 `ethclient` 的功能不在本篇文档中介绍，我们将详细介绍 `atlasclient` 特有的功能。

## 安装

```bash
go get github.com/mapprotocol/atlasclient
```

## 连接 atlas 网络

```go
package main

import (
	"context"
	"fmt"

	"github.com/mapprotocol/atlasclient"
)

func main() {
	// 连接 atlas 网络
	cli, err := atlasclient.Dial("https://rpc.maplabs.io")
	if err != nil {
		panic(err)
	}

	// 获取指定高度的区块
	block, err := cli.MAPBlockByNumber(context.Background(), big.NewInt(15960))
	if err != nil {
		panic(err)
	}
	fmt.Printf("block: %+v\n", block)
}
```

## Atlas 网络特有功能

### MAPBlockByHash

通过区块 hash 获取区块信息

```go
package main

import (
	"context"
	"fmt"

	"github.com/mapprotocol/atlasclient"
)

func main() {
	// 连接 atlas 网络
	cli, err := atlasclient.Dial("https://rpc.maplabs.io")
	if err != nil {
		panic(err)
	}

	block, err := cli.MAPBlockByHash(context.Background(), common.HexToHash("0xd30335352288aea176c33162d50f202017b3f5e745d81cdca343fa4b9b1ac93c"))
	if err != nil {
		panic(err)
	}
	fmt.Printf("block: %+v\n", block)

}
```

### MAPBlockByNumber

通过区块高度获取区块信息，如果 number 是 nil 则获取最新区块信息

```go
package main

import (
	"context"
	"fmt"

	"github.com/mapprotocol/atlasclient"
)

func main() {
	// 连接 atlas 网络
	cli, err := atlasclient.Dial("https://rpc.maplabs.io")
	if err != nil {
		panic(err)
	}

	block, err := cli.MAPBlockByNumber(context.Background(), big.NewInt(15960))
	if err != nil {
		panic(err)
	}
	fmt.Printf("block: %+v\n", block)
}
```

### MAPHeaderByNumber

通过区块高度获取区块头信息，如果 number 是 nil 则获取最新区块头信息

```go
package main

import (
	"context"
	"fmt"

	"github.com/mapprotocol/atlasclient"
)

func main() {
	// 连接 atlas 网络
	cli, err := atlasclient.Dial("https://rpc.maplabs.io")
	if err != nil {
		panic(err)
	}

	header, err := cli.MAPHeaderByNumber(context.Background(), big.NewInt(1))
	if err != nil {
		panic(err)
	}
	fmt.Printf("header: %+v\n", header)
}
```

### GetSnapshot

获取指定区块高度的快照信息，如果 number 是 nil 则获取最新快照信息

```go
package main

import (
	"context"
	"fmt"
	"math/big"

	"github.com/mapprotocol/atlasclient"
)

func main() {
	// 连接 atlas 网络
	cli, err := atlasclient.Dial("https://rpc.maplabs.io")
	if err != nil {
		panic(err)
	}

	snapshot, err := cli.GetSnapshot(context.Background(), big.NewInt(0))
	if err != nil {
		panic(err)
	}
	fmt.Printf("snapshot: %+v\n", snapshot)
}

```