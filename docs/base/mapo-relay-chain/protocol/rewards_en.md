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

In the second step, we will tally the number of signatures from each validator and convert it into a score (Score >=
0, <= 1) to participate in the reward calculation. If a validator fails to fulfill their duties, they will be subject to
a penalty mechanism. Therefore, each validator will receive a reward:

- ValidatorReceived = ( EpochReward - CommunityFundReward ) * StakingWeight * OwnActiveVotes / TotalActiveVotes +
- WorkWeight * OwnScore / TotalScores 。
- StakingWeight 验证人的质押权重（StakeWeight>0，StakeWeight<=1，默认值为1）
- WorkWeight 验证人的工作权重（WorkWeight = 1 - StakeWeight）。
- OwnActiveVotes 当前验证人激活的投票数。
- TotalActiveVotes 所有验证者收到的活跃投票。
- OwnScore
  当前验证者获得的分数。你的节点在线时间会直接影响这个参数。如果您的此参数小于其他验证器，您将受到惩罚，这将大幅削减您的奖励。 (
  0 <= 自己的得分 <= 1)
- TotalScores 所有验证者的分数总和。

分数由以下公式计算：

- NewScore = UptimeScore * AdjustmentSpeed + OldScore * (1 - 调整速度 )
- UptimeScore 这是 TotalMonitoredBlocks 完成的工作分数。
    - TotalMonitoredBlocks 是我们监控纪元正常运行时间的区块总数。
    - TotalMonitoredBlocks
      取值范围为 [EpochFirstBlock + lookbackWindowSize(defult = 12) -1,EpochLastBlock - BlocksToSkipAtEpochEnd(defult = 2)]
    - lookbackWindowSize 关于lookbackWindow的固定值，检查验证器是否在固定间隔内签名。
    - 如果您成功登录 [NowBlockNumber-lookbackWindow ,NowBlockNumber] ，我们将认为您当前的区块工作成功。
    - BlocksToSkipAtEpochEnd 表示从纪元结束开始在监控窗口上跳过的块数。目前我们跳过区块：
        - lastBlock => parentSeal 位于下一个 epoch 的 firstBlock 上
        - lastBlock - 1 =>parentSeal 位于 lastBlockOfEpoch 上，但 validatorScore 是在更新分数之前使用 lastBlockOfEpoch
          计算的 （lastBlock-1可以算，但是实现起来比较困难）
    - 关于要监视的第一个块：
      当前的 lookbackWindow 跨越 epoch 边界时，我们无法监控正常运行时间。
      因此，要监视的第一个块是从 firstBlockOfEpoch 开始的回溯窗口的最后一个块。
      所以 UptimeScore = SignedBlocks / TotalMonitoredBlocks 。
      SignedBlocks 验证者签名的区块数量，每次生成区块时都会检查该数量。
      首先，检查当前块是否在 BlocksToSkipAtEpochEnd 中。
      其次，检查当前区块审核期是否签署。如果存在则视为签名成功
      如果第一步和第二步成功 SignedBlocks 将加 1。
      AdjustmentSpeed 调整因子。这种方式将鼓励验证器稳定。（'AdjustmentSpeed' 默认值为 0.2）。
      OldScore 这是该验证人最后一个epoch的得分。（第一个分配epoch奖励OldScore为0）

### 第三步

第三步，由于验证者会将奖励分发给投票者，因此实际验证者将收到奖励：
ValidatorActualReceived = ValidatorReceived * Commission * OwnScore 。
Commission 验证器按与 ValidatorReceived 成比例抽取的比例值。
验证者的投票者将收到：

- VotersReward = ValidatorReceived - ValidatorActualReceived 。
  这些奖励（ VotersReward ）将返回投票池。这个操作相当于增加了每个选民的票数。投票者从投票池中抽取选票并获得相应奖励。
  每个投票者的奖励将根据投票者与验证者的投票比例获得。

## 奖励&赎回

目前每一个 epoch 的最后一个区块都会计算并发放奖励，奖励会直接增加到你对某个 validator 的活跃投票数量上。
如果你想将发放的奖励提取到你的账号，你需要进行赎回，有关赎回的具体步骤请参考[这里](/docs/base/mapo-relay-chain/example/how-to-withdraw.md)

## 扩展

### 案例一：验证人在服务期间被注销。

在这种情况下，我们在奖励时不会考虑他，其余验证者将平均分享他的奖励。

### 案例二：验证者重新注册。

当验证人被注销后，投票者仍然可以根据自己的意愿取消对注销验证人的投票。
如果选民不撤回选票，选民的选票将保留在投票区，等待验证人再次登记，这些票将再次返回给验证人。

## 执行

[EpochRewards])(https://github.com/mapprotocol/atlas-contracts/blob/main/contracts/governance/EpochRewards.sol) 合约管理计算
epoch 奖励。

## 相关主题

- [选举](/docs/base/mapo-relay-chain/protocol/election.md)
