## 什么是 epoch 奖励？

Epoch Rewards 类似于其他区块链中常见的区块奖励概念，在区块产生时铸造和分配新的 MAPO ，以创建多种激励措施。

Epoch 奖励在该 epoch 的最后一个区块中支付，用于：

1. 为验证者分配奖励
2. 向投票选出验证人的验证人的锁定 MAP 持有者分发奖励
3. 向社区基金支付协议基础设施补助金

每个 epoch 都会获得固定的奖励。前 2 年， EpochReward = 833,333 MAP 。

## 奖励发放

支付金额是在每个时期结束时通过三步过程确定的。

### 第一步

第一步，社区基金将获得固定比例的奖励
CommunityFundReward = CommunityFundMultiplier*EpochReward
CommunityFundMultiplier 默认值 = 0。

### 第二步

第二步，我们将统计每个验证人的签名数量，并将其转换为分数（ Sorce >=0,<=1），最终参与奖励的计算。
如果验证人未能履行职责，将通过惩罚机制进行惩罚。
因此每个验证者将获得奖励：

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
 todo

## 扩展

### 案例一：验证人在服务期间被注销。

在这种情况下，我们在奖励时不会考虑他，其余验证者将平均分享他的奖励。

### 案例二：验证者重新注册。

当验证人被注销后，投票者仍然可以根据自己的意愿取消对注销验证人的投票。
如果选民不撤回选票，选民的选票将保留在投票区，等待验证人再次登记，这些票将再次返回给验证人。

## 执行

[EpochRewards])(https://github.com/mapprotocol/atlas-contracts/blob/main/contracts/governance/EpochRewards.sol) 合约管理计算 epoch 奖励。