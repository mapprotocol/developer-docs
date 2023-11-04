# Setting up a private network

## Prerequisites

- Private key and address
- Building Atlas requires git, Go (version 1.14 or higher), and a C compiler
- Clone the code repository

```shell
git clone https://github.com/mapprotocol/atlas.git
```

## Build

We can start four nodes with different ports on a single host, or we can start four nodes on multiple hosts. The
following example demonstrates starting four nodes with different ports on a single host.

```shell
git checkout v1.1.5

make atlas
```

## Create an account

Atlas allows developers to use Ethereum accounts on the Atlas blockchain. If you don't have an account, you can create
one using the following command.

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

## Generating genesis.json file

To generate the genesis.json file, please refer
to [this](/docs/base/mapo-relay-chain/nodes/genesis-config_en.md#generating-the-genesisjson-file)

Now we will use the information of the four accounts generated in the previous step to generate the genesis.json file.
Fill in the corresponding key-value pairs in the `markerConfig.json` file with the information outputted when we created
the accounts. The value for AdminAddress can be one of the four accounts we created earlier or any other account.

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
    }
  ]
}
```

Then, generate the genesis.json file using the markerConfig.json file mentioned above, as follows:

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

After the command execution is complete, you can find the genesis.json file in the current directory.

## Initialize the nodes

Initialize the four validator nodes using the genesis file generated in the previous step.

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

## Start the nodes

Start the four corresponding nodes using the four accounts created earlier using the following command.

```shell
./atlas --datadir ./node-1 --networkid 110112 --port 20201 --mine --miner.validator 0xB16561A66B6439944DAf0388b5E6a2D3D0a49e12 --unlock 0xB16561A66B6439944DAf0388b5E6a2D3D0a49e12 console
./atlas --datadir ./node-2 --networkid 110112 --port 20202 --mine --miner.validator 0x0e2699Be2B47Dfd7560c4771A32A50b64F81293d --unlock 0x0e2699Be2B47Dfd7560c4771A32A50b64F81293d console
./atlas --datadir ./node-3 --networkid 110112 --port 20203 --mine --miner.validator 0x3c0ef282c8c62a44eA2FE8928b6bd89a16fD8252 --unlock 0x3c0ef282c8c62a44eA2FE8928b6bd89a16fD8252 console
./atlas --datadir ./node-4 --networkid 110112 --port 20204 --mine --miner.validator 0x41E4A55Ef06c1961B3e143357e24cA7f92e4DD03 --unlock 0x41E4A55Ef06c1961B3e143357e24cA7f92e4DD03 console

```

Please enter the above command in the command line and press Enter. You will see the following prompt:

```shell

......

INFO [10-09|18:16:28.639] Starting peer-to-peer node               instance=atlas/v1.1.5-stable/darwin-amd64/go1.20.7
INFO [10-09|18:16:28.759] New local node record                    seq=1,696,846,449,759 id=657ee01225705fca ip=127.0.0.1 udp=20201 tcp=20201
INFO [10-09|18:16:28.759] Started P2P networking                   self=enode://89b72450e02cf8a22dc66db717f4bab75b55961f2a474846f88615fd2be49bf406f3499842b39c5acd0d4ebc9fa72c29e27f1995a740d334c20b647e29a477b2@127.0.0.1:20201 maxdialed=33 maxinbound=67
INFO [10-09|18:16:28.762] IPC endpoint opened                      url=/Users/t/go_project/mapprotocol/atlas/node-1/atlas.ipc
Unlocking account 0x3BaB3BDe5ECC520134da32Bf7891578c108FC290 | Attempt 1/3
Password: 
```

At this point, you will need to enter the password corresponding to the account and press Enter. Congratulations, you
have successfully started one node. Then, you can start the remaining nodes in the same way.

If you want to start an RPC node, you can use the following method.

```shell
./atlas --datadir ./node-rpc init ./genesis.json

./atlas --datadir ./node-rpc --networkid 110112 --port 20205 --http --http.addr "0.0.0.0" --http.port 7445 --http.api eth,web3,net,debug,txpool,header,istanbul --http.corsdomain "*" console
```

## Connect the nodes

Before starting the nodes, make sure that the bootnode is running and accessible from the outside (try
telnet <ip> <port> to ensure it can be accessed). Then, use --bootnodes to point each subsequent Atlas node to the
bootnode for peer discovery. Specify the data storage directory using --datadir.

```shell
$ atlas --datadir=path/to/custom/data/folder --bootnodes=<bootnode-url>
```

To query the enode information of your node, you can use the following command:

```shell
admin.nodeInfo.enode

Output:
enode://89b72450e02cf8a22dc66db717f4bab75b55961f2a474846f88615fd2be49bf406f3499842b39c5acd0d4ebc9fa72c29e27f1995a740d334c20b647e29a477b2@127.0.0.1:20201
```

To connect to a node using the console, use the following command:

```shell
admin.addPeer("enode://89b72450e02cf8a22dc66db717f4bab75b55961f2a474846f88615fd2be49bf406f3499842b39c5acd0d4ebc9fa72c29e27f1995a740d334c20b647e29a477b2@127.0.0.1:20201")
admin.addPeer("enode://a4642d6eb2f1c69ee861f4146636df028a7eb328e233f620cc6838db474e94327bdcdc810d2f9c2fa30694764e71b4c7b5828f6e8df7a3f71f3eb781bb017a4e@127.0.0.1:20202")
admin.addPeer("enode://88c2fdd0189a33e3b8ee02a04a767c4792140c00c08de5d368b9aac578a0a36b5518aee5fcb695cd93c348237901a5c532f561170adc00903001e40ca3eff041@127.0.0.1:20203")
admin.addPeer("enode://88c2fdd0189a33e3b8ee02a04a767c4792140c00c08de5d368b9aac578a0a36b5518aee5fcb695cd93c348237901a5c532f561170adc00903001e40ca3eff041@127.0.0.1:20204")
```

## Check node status

After entering the command to connect to the node, you will start seeing some output. After a few minutes, you should see a line like this. This means that your node has connected to other nodes and will start generating blocks.

```shell
INFO [10-09|18:32:27.683] Looking for peers                        peercount=2 tried=5 static=4
INFO [10-09|18:32:37.582] Reset timer to resend RoundChange msg    address=0xB16561A66B6439944DAf0388b5E6a2D3D0a49e12 func=resetResendRoundChangeTi
mer cur_seq=23 cur_epoch=1 cur_round=0 des_round=5 state="Waiting for new round" address=0x2dC45799000ab08E60b7441c36fCC74060Ccbe11 timeout=17.5s
INFO [03-16|20:32:39.311] Looking for peers                        peercount=3 tried=0 static=4
```

You can also use `admin.peers` command in the console to see the connected nodes.