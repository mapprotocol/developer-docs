## Proof of Stake

`Atlas`, the MAP relay chain, is a proof of stake blockchain. Compared to proof of work systems like Bitcoin and
Ethereum, this eliminates the negative environmental impact, allowing for cheaper and faster transactions with finality
once completed. `Atlas` implements the Istanbul Byzantine Fault Tolerant (IBFT) consensus algorithm, where a set of
explicitly defined validator nodes broadcast signed messages among themselves in a series of steps to reach consensus
even in the presence of faulty or malicious behavior, even when up to one-third of the nodes are offline. When a quorum
of validators agrees, the decision becomes final.

## Validators

Validators collect transactions received from other nodes and execute any relevant smart contracts to form new blocks,
participating in the Byzantine Fault Tolerant (BFT) consensus protocol to advance the network state. Since the BFT
protocol can only scale to a few hundred participants and tolerate up to one-third of participants behaving maliciously,
the proof of stake mechanism allows for a limited set of nodes to fulfill this role.

## Staking Requirements

`Atlas` employs a proof of stake consensus mechanism that requires validators to lock MAPO tokens to participate in
block production. The current requirement is 1,000,000 MAPO.

## Election Process

During each epoch, at the generation of the last block, the election contract is called to select the set of validators
for the next epoch. The contract maintains a sorted list of validators' locked MAPO votes (pending or active). After
processing transactions and epoch rewards during the generation of the last block of each epoch, an election is
conducted to update the active set of validators. There is a minimum target and maximum upper limit for the number of
active validators to be chosen. If the minimum target is not met, the election is aborted, and no changes are made to
the validator set for that epoch.

## Validator Ranking and Rewards

Participants make these decisions by locking MAPO and voting for validators. Validator elections are held every epoch,
approximately every three days. The protocol allows for a maximum of 100 validators to be elected. At each epoch, each
elected validator must be re-elected to continue. Validators are selected based on the proportion of votes received by
each validator. If you hold MAPO or are a beneficiary of the Release MAPO contract, which allows for voting, you can
vote for validators. An individual account can split its locked MAPO balance to have pending votes between one and ten
validators. Once a governance proposal supporting rewards is passed by the community, the MAPO tokens locked and used
for voting for validators will receive epoch rewards each epoch.