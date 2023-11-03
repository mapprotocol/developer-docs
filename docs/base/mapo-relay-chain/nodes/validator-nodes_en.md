## What is a Validator Node

A validator node collects transactions received from other nodes and executes any relevant smart contracts to form new
blocks. It then participates in the Byzantine Fault Tolerance (BFT) consensus protocol to advance the network state. As
the BFT protocol can only scale to a few hundred participants and tolerate up to one-third of participants engaging in
malicious behavior, the proof-of-stake mechanism only allows a limited set of nodes to serve in this role.

## Staking

Atlas adopts a proof-of-stake consensus mechanism, and if you want to participate in block generation on the Atlas
network, you need to register as a validator. Currently, to become a validator, you need to stake 1,000,000 MAPO.

## Running a Validator Node

To run a validator node, you need an account registered in the validator set. To learn how to register as a validator,
please refer to [here](/docs/base/mapo-relay-chain/example/how-to-become-a-new-validator.md).

Run the following command to start a validator node:

```shell
atlas --datadir ./node --syncmode "full" --port 30321 --v5disc --mine --miner.validator 0x98efa292822eb7b3045c491e8ae4e82b3b1ac005 --unlock 0x98efa292822eb7b3045c491e8ae4e82b3b1ac005
```

## Related Topics

- [Election](/docs/base/mapo-relay-chain/protocol/election.md)
- [Reward](/docs/base/mapo-relay-chain/protocol/rewards.md)
- [How to become a new validator](/docs/base/mapo-relay-chain/example/how-to-become-a-new-validator.md)