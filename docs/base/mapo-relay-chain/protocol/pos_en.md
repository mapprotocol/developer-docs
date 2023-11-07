## Proof of Stake

The MAP Relay Chain (Atlas) is a proof of stake blockchain. In contrast to proof of work systems used by Bitcoin and
Ethereum, this eliminates the negative environmental impact. It allows users to engage in cheaper and faster
transactions, with the added benefit that once a transaction is completed, its outcome cannot be altered. The MAP Relay
Chain implements the Istanbul Byzantine Fault Tolerance (IBFT) consensus algorithm, wherein a defined set of validator
nodes broadcast signed messages amongst themselves in a series of steps. This enables consensus to be reached even in
the presence of up to one-third of the nodes being offline, faulty, or engaging in malicious behavior. When a quorum of
validators reaches an agreement, the decision becomes the final decision.

## Validators

Validators collect transactions received from other nodes and execute any relevant smart contracts to form new blocks,
participating in the Byzantine Fault Tolerant (BFT) consensus protocol to advance the network state. Since the BFT
protocol can only scale to a few hundred participants and tolerate up to one-third of participants behaving maliciously,
the proof of stake mechanism allows for a limited set of nodes to fulfill this role.

## Staking Requirements

`Atlas` employs a proof of stake consensus mechanism that requires validators to lock MAPO tokens to participate in
block production. The current requirement is 1,000,000 MAPO.

## About Elections

During the generation of the last block in each epoch, the election contract is called to select the validator set for
the next epoch. The contract maintains a sorted list of each validator's locked MAPO votes (pending or activated). After
processing transactions and epoch rewards during the generation of the last block in each epoch, an election is
conducted to update the active validator set. The number of active validators that can be selected has a minimum
target (1) and a maximum cap (100). If the minimum target is not met, the election will be aborted, and no changes will
be made to the validator set for that epoch.

## Validator Numbering and Rewards

Participants make these decisions by locking MAPO and voting for validators. Validator elections are conducted in each
epoch (approximately every three days). The protocol can elect up to 100 validators. In each new epoch, all elected
validators must be re-elected to continue. The election of validators is based on the proportion of votes received by
each validator. If you hold MAPO or are a beneficiary of the Release MAPO contract that allows voting, you can vote for
validators. A single account can split its locked MAPO balance to have pending votes between 1 and 10 validators. Once a
governance proposal supporting rewards is passed by the community, the MAPO locked and used for voting for validators
will receive rewards in each epoch.