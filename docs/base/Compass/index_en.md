# Compass

## Compass - the introduction of model and arch

### maintainer
![](maintainer.png)

### messenger
![](messenger.png)

## config of Compass
the compass config
```json
{
  "mapchain": {
    "id": "212",
    "endpoint": "http://18.142.54.137:7445",
    "from": "0xE0DC8D7f134d0A79019BEF9C2fd4b2013a64fCD6",
    "opts": {
      "mcs": "0x0ac4611305254cdd257beC56CB79CBeC720Cd02D",
      "lightnode": "0x000068656164657273746F726541646472657373",
      "http": "true",
      "gasLimit": "4000000000000",
      "maxGasPrice": "2000000000000",
      "syncIdList": "[34434]",
      "apiUrl" : "http://127.0.0.1:8080"
    }
  },
  "chains": [
    {
      "name": "pri-eth",
      "type": "ethereum",
      "id": "34434",
      "endpoint": "http://18.138.248.113:8545",
      "from": "0xE0DC8D7f134d0A79019BEF9C2fd4b2013a64fCD6",
      "opts": {
        "mcs": "0xcfc80beddb70f12af6da768fc30e396889dfce26",
        "lightnode": "0x80Be41aEBFdaDBD58a65aa549cB266dAFb6b8304",
        "http": "true",
        "gasLimit": "400000000000",
        "maxGasPrice": "200000000000",
        "syncToMap": "true"
      }
    }
  ],
  "other": {
    "monitor_url": "http:/slack...",
    "etcd": "...",
    "env": "example-compass-config"
  }
}
```
### main-config
```
{
      "name": "pri-eth",                                    // name of chain
      "type": "ethereum",                                   // type of chain，please refer refer to the table below for specific types
      "id": "34434",                                        // chain id
      "endpoint": "http://18.138.248.113:8545",             // chainendpoint 
      "from": "0xE0DC8D7f134d0A79019BEF9C2fd4b2013a64fCD6"  // user addr, use this addr send tranaction
}
```

|    chain     | type     |
|:------------:|----------|
| ethereum、map | ethereum |
|     bsc      | bsc      |
|  goerli、eth  | eth2     |
|   polygon    | matic    |
|     near     | near     |
|    klaytn    | klaytn   |
|    platon    | platon   |
|   conflux    | conflux  |

### Opts
```
{
    "mcs": "0x12345...",                                    // mcs addr
    "maxGasPrice": "0x1234",                                // max gasPrice
    "gasLimit": "0x1234",                                   // max gasLimit
    "gasMultiplier": "1.25",                                // gasPrice multitle
    "http": "true",                                         // chain endpoint use http
    "startBlock": "1234",                                   // startblock
    "blockConfirmations": "10"                              // block with the latest on the chain block how much difference
    "egsApiKey": "xxx..."                                   // apiKey, please check for details (https://www.ethgasstation.info/)
    "egsSpeed": "fast"                                      // gasPrice speed，For example "average", "fast", "fastest"
    "lightnode": "0x12345...",                              // lightnode addr
    "syncToMap": "true",                                    // block is sync to map
    "syncIdList": "[214]"                                   // map header sync to those chain，use chainId
    "event": "mapTransferOut(...)|depositOutToken(...)",    // mcs scanning event
}
```
### Other
```
{
    "monitor_url": "http:/slack...", // program monitor alarm addr，Now only support slack
    "etcd": "...",                   // regiter center addr, running，Will register with this address
    "env": "example-compass-config"  // flag of program running
  }
```

## Compass env and deploy
### env
need install Go 1.16+，gcc、make， if you need `near` chain，please install npm，login in to generate your account credentials，The machine requires more than 2C4G。

### linux deploy
1. git clone https://github.com/mapprotocol/compass
2. cd compass && make build
3. use `compass accounts import --privateKey {yourKey}` command import your account
4. if you want to run model of maintainer, you can use `compass maintainer --blockstore ./blockStore --config ./config.json` command to run (blockStore stores the historical progress for this time. The next time it runs, the historical progress will be read locally and start from this progress)
5. if you want to run model of messenger, you can use `compass messenger --blockstore ./blockStore --config ./config.json` command to run
### Docker deploy
1. make for Dockerfile，use `docker build -t compass:1.0.0 .` command
2. make after，user `docker images` command to check image
3. Use `docker run --name maintainer -d -e KEYSTORE_PASSWORD=$KEYSTORE_PASSWORD -v ${your config dir}:/root/config -v ${your account dir}:/root/keys -v ${your near account dir}:/root/.near-credentials compass:1.0.0 maintainer --config /root/config/config.json --blockstore /root/config/all/maintainer (It is recommended that the blockStore directory and the configuration directory be placed together.)` command to run images

## Compass secondary development - Define your own routing service based on compass
Now compass only generate proof，For more follow-up please pay attention to the official community

Please check for details (https://github.com/mapprotocol/compass-sdk)