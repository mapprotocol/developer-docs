## What are epoch rewards?

Epoch Rewards are similar to the concept of block rewards commonly seen in other blockchains, where new MAPO tokens are
minted and distributed at the creation of blocks to create various incentives.

Epoch rewards are paid out in the last block of that epoch and are used for:

1. Allocating rewards to validators
2. Distributing rewards to the locked MAPO holders of validators who were voted in
3. Paying protocol infrastructure grants to the community fund

Each epoch receives a fixed reward. For the first 2 years, EpochReward = 833,333 MAP.

## Reward Distribution

The payment amount is determined through a three-step process at the end of each epoch.

### Step 1

In the first step, the Community Fund will receive a fixed proportion of the reward.
CommunityFundReward = CommunityFundMultiplier * EpochReward
The default value for CommunityFundMultiplier is 0.

### Step 2

In the second step, we will tally the number of signatures from each validator and convert it into a score (0 <=
Score <= 1) to participate in the reward calculation. If a validator fails to fulfill their duties, they will be subject to
a penalty mechanism. Therefore, each validator will receive a reward:

- ValidatorReceived = ( EpochReward - CommunityFundReward ) * StakingWeight * OwnActiveVotes / TotalActiveVotes +
- WorkWeight * OwnScore / TotalScores 。
- StakingWeight: The stake weight of the validator (0 < StakeWeight <= 1, default value is 1).
- WorkWeight: The work weight of the validator (WorkWeight = 1 - StakeWeight).
- OwnActiveVotes: The number of active votes that the validator currently has.
- TotalActiveVotes: The total number of active votes received by all validators.
- OwnScore: The score obtained by the current validator. Your node's online time directly affects this parameter. If
  your score is lower than other validators, you will be penalized, which will significantly reduce your rewards. (0 <=
  OwnScore <= 1)
- TotalScores: The sum of scores for all validators.

The score is calculated using the following formula:

- NewScore = UptimeScore * AdjustmentSpeed + OldScore * (1 - AdjustmentSpeed)
- UptimeScore is the score for the work completed by TotalMonitoredBlocks.
    - TotalMonitoredBlocks is the total number of blocks that we monitor for the normal operation of the epoch.
    - TotalMonitoredBlocks has a range
      of [EpochFirstBlock + lookbackWindowSize (default = 12) - 1, EpochLastBlock - BlocksToSkipAtEpochEnd (default = 2)].
    - lookbackWindowSize is a fixed value for checking if validators have signed within a fixed interval.
    - If you have successfully logged in [NowBlockNumber-lookbackWindow, NowBlockNumber], we consider your current block
      work successful.
    - BlocksToSkipAtEpochEnd represents the number of blocks skipped on the monitoring window starting from the end of
      the epoch. Currently, we skip the following blocks:
        - lastBlock => parentSeal is on the firstBlock of the next epoch.
        - lastBlock - 1 => parentSeal is on lastBlockOfEpoch, but validatorScore is calculated using lastBlockOfEpoch
          before updating the score (lastBlock-1 can be counted, but it is more difficult to implement).
    - Regarding the first block to be monitored:
      When the current lookbackWindow spans across epoch boundaries, we cannot monitor the normal operation time.
      Therefore, the first block to be monitored is the last block of the lookback window starting from
      firstBlockOfEpoch.
      So UptimeScore = SignedBlocks / TotalMonitoredBlocks.
      SignedBlocks is the number of blocks signed by the validator, which is checked every time a block is generated.
      First, check if the current block is in BlocksToSkipAtEpochEnd.
      Second, check if the current block is signed in the audit period. If it is, it is considered a successful
      signature.
      If both steps 1 and 2 succeed, SignedBlocks is increased by 1.
      AdjustmentSpeed is the adjustment factor. This way, validators are encouraged to be stable. ('AdjustmentSpeed' has
      a default value of 0.2).
      OldScore is the score of the validator in the last epoch. (The first allocated epoch reward has OldScore as 0).

### 第三步

In step three, because the validator will distribute the reward to voter, the actual validator will receive the reward:

ValidatorActualReceived = ValidatorReceived * Commission * OwnScore.
Commission A proportional value drawn by the validator in proportion to the ValidatorReceived.

The validator's voters will receive:
VotersReward = ValidatorReceived - ValidatorActualReceived.

These rewards(VotersReward) will be returned to the voting pool. This operation is equivalent to an increase in the
number of votes per voter. Voters draw their votes from the voting pool and get corresponding rewards.
Per voter's reward will be obtained according to the voting proportion of voter to validator.

## Rewards and withdraw

Currently, rewards are calculated and distributed at the end of each epoch, and the rewards are directly added to your
active voting count for a particular validator.
If you want to withdraw the distributed rewards to your account, you need to withdraw. Please refer
to [here](/docs/base/mapo-relay-chain/example/how-to-withdraw_en.md) for specific redemption steps.

## Extensions

### Scenario 1: Validator is deregistered during their service period.

In this case, they will not be considered for rewards during the reward distribution, and the remaining validators will
evenly share their rewards.

### Scenario 2: Validator re-registers.

When a validator is deregistered, voters can still withdraw their votes for the deregistered validator at their own
will.
If a voter does not withdraw their vote, their vote will be kept in the voting pool and wait for the validator to
re-register, and the vote will be returned to the validator again.

## Implementation

The [EpochRewards](https://github.com/mapprotocol/atlas-contracts/blob/main/contracts/governance/EpochRewards.sol)
contract manages the calculation of epoch rewards.

## Related Topics

- [Election](/docs/base/mapo-relay-chain/protocol/election_en.md)
