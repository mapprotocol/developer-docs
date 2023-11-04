## How to become a new validator advanced

To ensure the security of your assets, we require you to set up some necessary identity verification parameters to
become a validator. We also have corresponding thresholds to filter out those who are truly committed to contributing to
the chain. Of course, we will reward those individuals accordingly. The following steps are considered your initial
setup as a validator, as you only need to perform these steps once to become a validator. You do not need to perform
these steps a second time unless you unregister as a validator or cancel the corresponding operation to avoid wasting
your gas fees.

### Step 1: Create Account

In this step, you need to store your identity information in the corresponding management contract, which will manage
your account, keys, and metadata. The purpose of this step is to ensure the security of the locked MAPO by authorizing
an alternative key for signing, voting, and validating. By doing so, you can continue to participate in the protocol
while retaining access to the locked MAPO in storage. You need to use the `createAccount` command to perform the above
operations. For more detailed information about the `createAccount` command, please refer
to [here](/docs/base/mapo-relay-chain/marker/common_en.md#createaccount).

### Step 2: Authorization

Authorize an address to sign consensus messages on behalf of your account. This authorized address is called the signer.
As the name suggests, the signer is only responsible for signing, and your rewards will not be issued to the signer but
to the account created in the previous step.

### Step 3: Lock MAP

The threshold for becoming a validator is to lock 1,000,000 MAPO into the corresponding management smart contract. This
locked MAP will be used for future penalties and is one of the conditions for election. You need to use the `LockedMAP`
command to perform the above operation. For more detailed information about the `LockedMAP` command, please refer
to [here](/docs/base/mapo-relay-chain/marker/common_en.md#lockedmap).

### Step 4: Validator Registration

This step is a key step in registering as a new validator. You need to use the `register` command to perform the above
operation. For more detailed information about the `register` command, please refer
to [here](/docs/base/mapo-relay-chain/marker/validator_en.md#register). At this point, you will have successfully
registered as a validator. Next, you can try to vote for yourself. For more information on how to vote, please refer
to [here](/docs/base/mapo-relay-chain/marker/vote_en.md#vote).

### Step 5: Voting

A validator must have at least 0.001 proportion of the total votes to be considered for election. Therefore, a validator
cannot have zero votes. We can either vote for ourselves using our validator account or have other validators or voters
vote for us. Since we have locked 1,000,000 MAP in Step 3, it is wise to vote for ourselves.

## Example

### Start Your Node

Before starting the node, you need to build Atlas. Please refer
to [here](/docs/base/mapo-relay-chain/nodes/run-a-node_en.md#clone-the-code-repository-and-build) for instructions on
how
to build Atlas.

You will need two keystore files, one for the account used for staking, called `account`, and one for the signer used
for signing consensus blocks.

If you want the Atlas node to run in the background without hanging up, you can use `nohup` and `&` together, or
use `screen` or similar. Here we will demonstrate using `screen`.

`account.json`: keystore file for the account
`signer.json`: keystore file for the signer

`--miner.validator`: specify the address of the signer
`--port 30321`: make sure the port is open on the firewall

```shell
./atlas --datadir ./node --syncmode "full" --port 30321 --v5disc --mine --miner.validator 0x98efa292822eb7b3045c491e8ae4e82b3b1ac005 --unlock 0x98efa292822eb7b3045c491e8ae4e82b3b1ac005

INFO [08-01|16:15:45.369] Bumping default cache on mainnet         provided=1024 updated=4096
INFO [08-01|16:15:45.370] Maximum peer count                       ETH=50 LES=0 total=50
INFO [08-01|16:15:45.371] Set global gas cap                       cap=50,000,000
INFO [08-01|16:15:45.371] Allocated trie memory caches             clean=614.00MiB dirty=1024.00MiB
INFO [08-01|16:15:45.371] Allocated cache and file handles         database=/Users/t/data/atlas-1/atlas/chaindata cache=2.00GiB handles=5120
INFO [08-01|16:15:45.722] Opened ancient database                  database=/Users/t/data/atlas-1/atlas/chaindata/ancient readonly=false
......

> net
{
  listening: true,
  peerCount: 6,
  version: "22776",
  getListening: function(callback),
  getPeerCount: function(callback),
  getVersion: function(callback)
}

......

INFO [08-01|16:40:40.796] Finalized                                func=Finalize        block=1 epochSize=50000 duration="333.422µs" lastInEpoch=false
INFO [08-01|16:40:40.797] Imported new chain segment               blocks=1 txs=0 mgas=0.000 elapsed=5.391ms mgasps=0.000 number=1 hash=62d029..14f4c1 age=1mo4d23h dirty=7.07KiB
INFO [08-01|16:40:51.790] Finalized                                func=Finalize        block=2 epochSize=50000 duration="106.969µs" lastInEpoch=false
INFO [08-01|16:40:51.790] Imported new chain segment               blocks=1 txs=0 mgas=0.000 elapsed=4.913ms mgasps=0.000 number=2 hash=c2b741..35869d age=1mo4d23h dirty=10.79KiB
INFO [08-01|16:40:53.892] Finalized                                func=Finalize        block=3 epochSize=50000 duration="104.285µs" lastInEpoch=false
INFO [08-01|16:40:53.893] Finalized                                func=Finalize        block=4 epochSize=50000 duration="105.568µs" lastInEpoch=false
```

After starting the node, it will connect to the network through built-in bootstrap nodes and synchronize block data.

### Generating ECDSA Signature

Generate an ECDSA signature signed by the signer account.

```shell
./marker makeECDSASignatureFromSigner --target 0x73bc690093b9dd0400c91886184a60cc127b2c33 --signerPriv 040939e5...604b6f25

INFO [08-26|17:31:49.422] === makeECDSASignatureFromSigner === 
INFO [08-26|17:31:49.422] === signer  ===                          account=0x26654Eb0Bb935DCE4a34DAA3e14c67662a8Aa1f8
INFO [08-26|17:31:49.422] ECDSASignature                           result=0x59dff185...32f0d700
```

### Generating Proof

Generate a certificate using the signer private key.

```shell
./marker generateSignerProof --validator 0x73bc690093b9dd0400c91886184a60cc127b2c33 --signerPriv 040939e5...604b6f25

INFO [08-26|17:32:23.950] generateBLSProof                         validator=0x01ccDcd1aE63a2C6B4c3493983dc6400C63729Ad signerPrivate=040939e5...604b6f25
INFO [08-26|17:32:23.955] === makeBLSProofOfPossessionFromSigner === 
INFO [08-26|17:32:23.958] generateBLSProof                         proof=0xf90149b8...0e56f0ab1
```

### Create an account

```shell
./marker createAccount --rpcaddr https://rpc.maplabs.io --keystore ./account.json --name "validator"

INFO [07-08|14:54:28.097] Create account                           func=createAccount address=0x73bC690093b9dD0400c91886184A60cC127b2c33 name=validator
INFO [07-08|14:54:28.097] === create Account === 
INFO [07-08|14:54:28.102] TxInfo                                   func=sendContractTransaction TX data nonce =0  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =1,098,789
INFO [07-08|14:54:28.103] Please waiting                           func=getResult                txHash =0xa8d9dfb5b74720a940e904972784225d605ebb111fe92b87184ccb3a54ffd452
INFO [07-08|14:54:30.726] Transaction Success                      func=queryTx                 block Number=9
INFO [07-08|14:54:30.726] === setName name === 
INFO [07-08|14:54:30.731] TxInfo                                   func=sendContractTransaction TX data nonce =1  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =1,098,789
INFO [07-08|14:54:30.732] Please waiting                           func=getResult                txHash =0xf26f4f8b4a7f6ab44a70fe474162fe22b1bcd7694b4c1a42ed0b1a70d062d5ee
INFO [07-08|14:54:35.779] Transaction Success                      func=queryTx                 block Number=10
INFO [07-08|14:54:35.779] === setAccountDataEncryptionKey === 
INFO [07-08|14:54:35.784] TxInfo                                   func=sendContractTransaction TX data nonce =2  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =1,098,789
INFO [07-08|14:54:35.785] Please waiting                           func=getResult                txHash =0x430533536a6305b603c9abd6282a4fec359c7f42b8dbd78cf4d3f340ec9bf31b
INFO [07-08|14:54:40.224] Transaction Success                      func=queryTx                 block Number=11
```

### Use signature authorization

Authorize using ECDSA signature(0x59dff185...32f0d700) signed by signer

signer`s account：0x98efa292822eb7b3045c491e8ae4e82b3b1ac005
signer's private key：8df920b696ef3f5fdcf01624405ea8236b2b4907766ad61d42ce877df05f8bca

```shell
./marker authorizeValidatorSignerBySignature --rpcaddr http://127.0.0.1:7445 --keystore ./account.json --signer 0x26654eb0bb935dce4a34daa3e14c67662a8aa1f8 --signature 0x59dff185...32f0d700

INFO [07-08|14:55:00.015] authorizeValidatorSignerBySignature      signer=0x26654eb0bb935dce4a34daa3e14c67662a8aa1f8    signature=0x59dff185...32f0d700
INFO [07-08|14:55:00.032] Please waiting                           func=getResult                 txHash =0xb73a1376e661d523e44b87c37e2e03cc36534d3a550808245f263aaad358b0ad
INFO [07-08|14:55:05.078] Transaction Success                      func=queryTx                  block Number=16
```

### Locking MAPO

```shell
./marker lockedMAP --rpcaddr https://rpc.maplabs.io --keystore ./account.json --lockedNum 1000000

INFO [07-08|14:54:49.141] === Lock  gold === 
INFO [07-08|14:54:49.141] Lock  gold                               amount=1000000000000000000000000
INFO [07-08|14:54:49.148] TxInfo                                   func=sendContractTransaction TX data nonce =3  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =1,098,789
INFO [07-08|14:54:49.150] Please waiting                           func=getResult                txHash =0x698140b0ad8677706a4d10d3c5c72f15a8e143623be84a6ed514990fe3f5e5f3
INFO [07-08|14:54:50.765] Transaction Success                      func=queryTx                 block Number=13
```

### Registering a Validator Using Proof

Register the validator using a proof generated by the signer's private key.

```shell
./marker registerByProof --rpcaddr http://127.0.0.1:7445 --keystore ./account.json --proof 0xf90149b8...0e56f0ab1 --commission 150000

INFO [08-26|17:32:25.055] registerValidatorByProof                 commission=150,000
INFO [08-26|17:32:25.548] === getTotalVotesForValidator ===        admin=0x73bc690093b9dd0400c91886184a60cc127b2c33
INFO [08-26|17:32:25.974] === getTotalVotesForValidator ===        result=0
INFO [08-26|17:32:26.011] TxInfo                                   func=sendContractTransaction TX data nonce =7  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =1,098,789
INFO [08-26|17:32:26.119] Please waiting                           func=getResult                txHash =0xfb0487e7196df7489e90ab91217251da5fc7d07b2bc2d2f4ea67966a506a4cd6
INFO [08-26|17:32:27.241] Transaction Success                      func=queryTx                 block Number=194
```

### Verify

After completing the registration steps for a validator, let's now verify if you have become a validator.

```shell
./marker getTotalVotesForEligibleValidators --rpcaddr https://rpc.maplabs.io

INFO [07-08|15:10:03.301] Validator:                               addr=0xeA9efaA232A4567EaC21C8C096f8BfF84595A244 vote amount=1,000,000,000,000,000,000,000,000
INFO [07-08|15:10:03.301] Validator:                               addr=0x6ACdC02223100189d82A958d888F54fA27d60e8A vote amount=1,000,000,000,000,000,000,000,000
INFO [07-08|15:10:03.301] Validator:                               addr=0xA53516D49A72019692Ac69cB42641942597654f6 vote amount=1,000,000,000,000,000,000,000,000
INFO [07-08|15:10:03.301] Validator:                               addr=0x5d643Dfb9ae372ce4Fdbc80890156E2CD8290846 vote amount=1,000,000,000,000,000,000,000,000
INFO [07-08|15:10:03.301] Validator:                               addr=0x73bC690093b9dD0400c91886184A60cC127b2c33 vote amount=0
```

### Voting

The number of votes cannot exceed the locked votes.

For more information about voting and elections, please click on the following links:
[Voting](/docs/base/mapo-relay-chain/marker/vote_en.md#vote)
[Elections](/docs/base/mapo-relay-chain/protocol/election_en.md)

```shell
./marker vote --rpcaddr https://rpc.maplabs.io --keystore ./account.json --target 0x73bc690093b9dd0400c91886184a60cc127b2c33 --voteNum 1000000

INFO [07-08|15:11:13.693] === vote Validator ===                   admin=0x73bc690093b9dd0400c91886184a60cc127b2c33 voteTargetValidator=0x73bC690093b9dD0400c91886184A60cC127b2c33 vote MAP Num=1000000
INFO [07-08|15:11:13.709] TxInfo                                   func=sendContractTransaction TX data nonce =4  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =1,098,789
INFO [07-08|15:11:13.710] Please waiting                           func=getResult                txHash =0x8ec67797871ea313f7beea33900db8f680ddf2d01f35894c65e1212151729747
INFO [07-08|15:11:15.123] Transaction Success                      func=queryTx                 block Number=210
```

### Validating the Vote

Now, let's validate if the vote is valid.

```shell
./marker getTotalVotesForEligibleValidators --rpcaddr https://rpc.maplabs.io

INFO [07-08|15:21:45.881] Validator:                               addr=0xeA9efaA232A4567EaC21C8C096f8BfF84595A244 vote amount=1,000,000,000,000,000,000,000,000
INFO [07-08|15:21:45.881] Validator:                               addr=0x6ACdC02223100189d82A958d888F54fA27d60e8A vote amount=1,000,000,000,000,000,000,000,000
INFO [07-08|15:21:45.881] Validator:                               addr=0xA53516D49A72019692Ac69cB42641942597654f6 vote amount=1,000,000,000,000,000,000,000,000
INFO [07-08|15:21:45.881] Validator:                               addr=0x5d643Dfb9ae372ce4Fdbc80890156E2CD8290846 vote amount=1,000,000,000,000,000,000,000,000
INFO [07-08|15:21:45.881] Validator:                               addr=0x73bC690093b9dD0400c91886184A60cC127b2c33 vote amount=1,000,000,000,000,000,000,000,000

```

From the results, it appears that I have successfully voted for myself, but it's not enough. We need to call the RPC in
the next epoch to ultimately determine if we have been selected as a validator eligible for block production. Here's how
it looks:

```shell
curl -X POST -H "Content-Type: application/json" -d '{"jsonrpc":"2.0","method":"istanbul_getValidators","params":[],"id":1}' https://rpc.maplabs.io

# Output
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": [
        "0x5d643dfb9ae372ce4fdbc80890156e2cd8290846",
        "0xa53516d49a72019692ac69cb42641942597654f6",
        "0x6acdc02223100189d82a958d888f54fa27d60e8a",
        "0xea9efaa232a4567eac21c8c096f8bff84595a244",
        "0x98efa292822eb7b3045c491e8ae4e82b3b1ac005" # our signer
    ]
}
```
