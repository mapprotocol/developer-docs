`atlasclient` is forked from `ethclient`. It inherits most of the functionality of `ethclient` and adds functionality
specific to the Atlas network.

`atlasclient` is a client for interacting with the Atlas network. It provides a convenient way to connect to the Atlas
network, send transactions, interact with smart contracts, and retrieve blockchain data.

Features of `atlasclient` include:

1. Connecting to an Atlas network: You can connect to a specific Atlas network, such as the mainnet or a testnet,
   using the client.
2. Retrieving blockchain data: You can fetch information about blocks, transactions, and account balances from the
   Atlas blockchain.
3. Sending transactions: You can create and send transactions to perform actions on the Atlas network, such as
   transferring ether or interacting with smart contracts.
4. Interacting with smart contracts: You can deploy smart contracts, call their functions, and retrieve their state
   using the client.**

The functions of `atlasclient` inherited from `ethclient` are not introduced in this document. We will introduce the
unique functions of `atlasclient` in detail.

## Install

```bash
go get github.com/mapprotocol/atlasclient
```

## Connect to atlas network

```go
package main

import (
	"context"
	"fmt"

	"github.com/mapprotocol/atlasclient"
)

func main() {
	// connect to atlas network
	cli, err := atlasclient.Dial("https://rpc.maplabs.io")
	if err != nil {
		panic(err)
	}

	// get block info at a specified height
	block, err := cli.MAPBlockByNumber(context.Background(), big.NewInt(15960))
	if err != nil {
		panic(err)
	}
	fmt.Printf("block: %+v\n", block)
}
```

## Atlas network specific features

### MAPBlockByHash

Obtain block information through block hash.

```go
package main

import (
	"context"
	"fmt"

	"github.com/mapprotocol/atlasclient"
)

func main() {
	// connect to atlas network
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

Get the block information through the block height. If number is nil, get the latest block information.

```go
package main

import (
	"context"
	"fmt"

	"github.com/mapprotocol/atlasclient"
)

func main() {
	// connect to atlas network
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

Get the block header information through the block height. If number is nil, get the latest block header information.

```go
package main

import (
	"context"
	"fmt"

	"github.com/mapprotocol/atlasclient"
)

func main() {
	// connect to atlas network
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

Get the snapshot information of the specified block height. If number is nil, get the latest snapshot information.

```go
package main

import (
	"context"
	"fmt"
	"math/big"

	"github.com/mapprotocol/atlasclient"
)

func main() {
	// connect to atlas network
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