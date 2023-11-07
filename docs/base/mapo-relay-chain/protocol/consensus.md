伊斯坦布尔拜占庭容错（IBFT）共识受到 [Castro-Liskov 99](https://pmg.csail.mit.edu/papers/osdi99.pdf) 论文的启发。
IBFT 继承了原始 PBFT，采用了 3 阶段共识：PRE-PREPARE、PREPARE 和 COMMIT。系统在 N 个验证者网络中最多可以容忍 F 个故障节点，其中
N = 3F + 1。 实现

## 术语

- 验证者（Validator）：区块验证参与者。
- 提议者（Proposer）：在共识轮中被选中提出区块的验证参与者。
- 轮（Round）：共识轮。一个轮次从提议者创建一个区块提议开始，以区块的承诺或轮次变更结束。
- 提议（Proposal）：正在进行共识处理的新区块生成提议。
- 序号（Sequence）：提议的序号。序号应大于所有先前的序号。目前，每个提议的区块高度即为其相关序号。
- Backlog：用于保存未来共识消息的存储。
- 轮次状态（Round state）：特定顺序和轮次的共识消息，包括预备消息、准备消息和提交消息。
- 共识证明（Consensus proof）：区块的承诺签名，可以证明该区块已经通过了共识过程。
- 快照（Snapshot）：上一个 epoch 的验证者投票状态。

## 共识

IBFT
共识协议从轮次0开始，验证者以轮询的方式选择提议者。然后，提议者会提出一个新的区块提议，并通过PRE-PREPARE消息进行广播。接收到提议者的PRE-PREPARE消息后，其他验证者会验证传入的提议，并进入准备前状态，并广播PREPARE消息。这一步是为了确保所有验证者在同一序号和同一轮次上进行工作。当验证者从其他验证者接收到至少ceil(
2N/3)
个PREPARE消息时，验证者切换到准备状态并广播COMMIT消息。这一步是通知其他验证者它接受了该提议的区块，并将该区块插入到区块链中。最后，验证者等待ceil(
2N/3)个COMMIT消息进入已提交状态，然后将该区块追加到区块链。

伊斯坦布尔BFT协议中的区块是最终的，这意味着没有分叉，并且任何有效的区块必须在主链中的某个位置。为了防止错误节点从主链生成完全不同的链，每个验证者在将区块插入链之前，在区块头的extraData字段中追加ceil(
2N/3)
个接收到的COMMIT签名。因此，所有区块都是自验证的。然而，动态的extraData会导致区块哈希计算的问题。由于来自不同验证者的相同区块可能具有不同的COMMIT签名集合，因此相同的区块也可能具有不同的区块哈希。为了解决这个问题，我们通过排除COMMIT签名部分来计算区块哈希。因此，我们仍然可以保持区块/区块哈希的一致性，并在区块头中放置共识证明。

### 共识状态

伊斯坦布尔BFT是一个状态机复制算法。每个验证者维护一个状态机副本，以达到区块共识。IBFT共识中的各个状态包括：

- NEW ROUND：提议者发送新的区块提议。验证者等待PRE-PREPARE消息。
- PRE-PREPARED：一个验证者接收到PRE-PREPARE消息并广播PREPARE消息。然后它等待至少ceil(2N/3)个PREPARE或COMMIT消息。
- PREPARED：一个验证者接收到至少ceil(2N/3)个PREPARE消息并广播COMMIT消息。然后它等待至少ceil(2N/3)个COMMIT消息。
- COMMITTED：一个验证者接收到至少ceil(2N/3)个COMMIT消息，并能够将提议的区块插入到区块链中。
- FINAL COMMITTED：一个新的区块成功地插入到区块链中，验证者准备进行下一轮。
- ROUND CHANGE：一个验证者正在等待相同提议轮次编号的至少ceil(2N/3)个ROUND CHANGE消息。

## 状态转换

![State Transitions](state-transitions.jpg)

### 状态转换

- NEW ROUND -> PRE-PREPARED:
    - 提议者从交易池中收集交易。
    - 提议者生成一个区块提案并广播给验证者。然后进入 PRE-PREPARED 状态。
    - 每个验证者在接收到 PRE-PREPARE 消息时进入 PRE-PREPARED 状态，并满足以下条件：
        - 区块提案来自有效的提议者。
        - 区块头有效。
        - 区块提案的序列和轮次与验证者的状态匹配。
    - 验证者向其他验证者广播 PREPARE 消息。

- PRE-PREPARED -> PREPARED:
    - 验证者收到 ceil(2N/3) 个有效的 PREPARE 消息后进入 PREPARED 状态。有效的消息满足以下条件：
        - 匹配的序列和轮次。
        - 匹配的区块哈希。
        - 消息来自已知的验证者。
    - 验证者在进入 PREPARED 状态后广播 COMMIT 消息。

- PREPARED -> COMMITTED:
    - 验证者收到 ceil(2N/3) 个有效的 COMMIT 消息后进入 COMMITTED 状态。有效的消息满足以下条件：
        - 匹配的序列和轮次。
        - 匹配的区块哈希。
        - 消息来自已知的验证者。

- COMMITTED -> FINAL COMMITTED:
    - 验证者将 ceil(2N/3) 个承诺签名附加到 extraData，并尝试将区块插入区块链。
    - 当插入成功时，验证者进入 FINAL COMMITTED 状态。

- FINAL COMMITTED -> NEW ROUND:
    - 验证者选择一个新的提议者，并开始一个新的轮次计时器。

### 轮次变更流程

- 三种条件可以触发轮次变更：
    - 轮次变更计时器超时。
    - 无效的 PREPREPARE 消息。
    - 区块插入失败。
- 当验证者注意到上述条件之一适用时，它会广播一个 ROUND CHANGE 消息，并附带建议的轮次号码，然后等待其他验证者的 ROUND CHANGE
  消息。建议的轮次号码根据以下条件选择：
    - 如果验证者已经收到来自同行的 ROUND CHANGE 消息，则选择具有 F + 1 个 ROUND CHANGE 消息的最大轮次号码。
    - 否则，选择当前轮次号码加一作为建议的轮次号码。
- 每当验证者在相同的建议轮次号码上收到 F + 1 个 ROUND CHANGE 消息时，它将收到的消息与自己的消息进行比较。如果收到的消息较大，则验证者再次广播带有收到的轮次号码的
  ROUND CHANGE 消息。
- 当验证者在相同的建议轮次号码上收到 ceil(2N/3) 个 ROUND CHANGE 消息时，验证者退出轮次变更循环，计算新的提议者，然后进入
  NEW ROUND 状态。
- 另一个使验证者跳出轮次变更循环的条件是通过对等节点同步收到验证的区块。

### 提议者选择

目前我们支持两种策略：轮流和固定提议者。

- 轮流：轮流是默认的提议者选择策略。在这种设置下，提议者会在每个区块和轮次变更时更换。
- 固定提议者：在固定提议者设置中，提议者只会在轮次变更发生时更换。

### 验证者列表投票

伊斯坦布尔BFT使用与Clique类似的验证者投票机制，并从Clique EIP中复制了大部分内容。每个时期的交易都会重置验证者投票，这意味着任何待定的添加/删除验证者的投票都会被重置。

对于所有交易块：

- 提议者可以投票提议更改验证者列表。
- 每个目标受益人只保留一个来自单个验证者的最新提议。
- 投票在链的进展中实时计算（允许并发提议）。
- 达到多数共识 VALIDATOR_LIMIT 的提议立即生效。
- 无效的提议不会因为客户端实现简单而受到处罚。
- 提议生效意味着丢弃该提议的所有待定投票（赞成和反对）。

### 未来消息和积压消息

在异步网络环境中，可能会收到当前状态无法处理的未来消息。例如，验证者可能会在 NEW ROUND 上收到 COMMIT
消息。我们将这种消息称为“未来消息”。当验证者收到未来消息时，它会将消息放入积压消息中，并在可能的时候尽力处理。

### 区块哈希、提议者签名和已委托签名

由于以下原因，伊斯坦布尔的区块哈希计算与ethash的区块哈希计算不同：

- 提议者需要将提议者的签名放入 extraData 中，以证明该区块是由选择的提议者签名的。
- 验证者需要将 ceil(2N/3) 个已提交签名作为共识证明放入 extraData 中，以证明该区块已经通过共识。

计算仍然类似于ethash的区块哈希计算，只是我们需要处理 extraData。我们按照以下方式计算字段：

#### 提议者签名计算

在进行提议者签名计算时，已提交的签名仍然未知，因此我们在计算签名时将这些未知的部分留空。计算方法如下：

- 提议者签名：SignECDSA(Keccak256(RLP(Header)), PrivateKey)
- PrivateKey：提议者的私钥。
- Header：与ethash头部相同，只是extraData不同。
- extraData：vanity | RLP(IstanbulExtra)，其中在IstanbulExtra中，CommittedSeal和Seal是空数组。

#### 区块哈希计算

在计算区块哈希时，我们需要排除已提交的签名，因为这些数据在不同的验证者之间是动态的。因此，在计算哈希时，我们将CommittedSeal设置为空数组。计算方法如下：

- Header：与ethash头部相同，只是extraData不同。
- extraData：vanity | RLP(IstanbulExtra)，其中在IstanbulExtra中，CommittedSeal是空数组。

#### 共识证明

在将区块插入区块链之前，每个验证者都需要从其他验证者收集 ceil(2N/3)
个已提交的签名来组成一个共识证明。一旦收到足够的已提交签名，验证者将填充IstanbulExtra中的CommittedSeal字段，重新计算extraData，然后将区块插入区块链。请注意，由于已提交的签名可以因不同的来源而不同，在计算区块哈希时我们排除了该部分，与前面的部分相同。

### 委托签名计算：

每个验证者通过使用其私钥对哈希和COMMIT_MSG_CODE消息代码进行签名来计算委托签名。计算方法如下：

- 已提交签名：SignECDSA(Keccak256(CONCAT(Hash, COMMIT_MSG_CODE)), PrivateKey)。
- CONCAT(Hash, COMMIT_MSG_CODE)：将区块哈希和COMMIT_MSG_CODE字节进行连接。
- PrivateKey：签名验证者的私钥。