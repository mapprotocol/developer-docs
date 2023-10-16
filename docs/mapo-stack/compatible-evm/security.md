---
title: 智能合约安全性
description: 安全的MAPO-Relay-Chain智能合约构建准则概述
lang: zh
---

智能合约极为灵活，能够控制大量的价值和数据，并在区块链上运行基于代码的不可改变逻辑。 因而，一个由无需信任的去中心化应用程序构成的生态系统应运而生且充满活力，它具备了许多传统系统所没有的优势。 同时，这也给攻击者提供了利用智能合约中的漏洞来获利的机会，已部署的合约代码*通常*无法更改因而不能给安全问题打补丁，并且由于这种不可变性，从智能合约中盗取的资产极难追踪并且绝大多数无法挽回。。以下`MAPO-Relay-Chain`统称为MAPO.

## 前言 {#prerequisites}

在开始研究安全性问题之前，请确保自己已经熟悉[智能合约开发的基础知识](/docs/mapo-stack/compatible-evm/index.md#智能合约smart-contracts)。

## 安全智能合约的构建准则 {#smart-contract-security-guidelines}

### 1. 设计合理的访问控制 {#design-proper-access-controls}

在智能合约中，带有 `public` 或 `external` 标记的函数可以被任何外部帐户 (EOA) 或者合约帐户调用。 如果你希望他人与你的合约交互，就必须为函数指定公共可见性。 然而，标记为 `private` 的函数只能被智能合约内部的函数调用，外部帐户无法调用。 为每个网络用户提供合约函数的访问权限会造成问题，尤其是当这种访问意味着任何人都能执行敏感操作（比如铸币）的情况。

为了防止未经授权使用智能合约函数，有必要实现安全访问控制。 访问控制机制将使用智能合约中某些特定函数的能力限定给经过核准的实体，例如负责管理合约的帐户。 两种模式有助于在智能合约中实现访问控制，**所有权模式**和**基于角色的控制**：

#### 所有权模式 {#ownable-pattern}

在所有权模式中，在合约创建过程中将地址设置为合约的“所有者”。 受保护的函数都分配有 `OnlyOwner` 修饰符，这样可以确保合约在执行函数之前验证调用地址的身份。 从合约所有者以外的其他地址调用受保护的函数，始终会被回滚，阻止不必要的访问。

#### 基于角色的访问控制 {#role-based-access-control}

在智能合约中将一个地址注册成 `Owner` 会引入中心化风险，并代表一种单点故障。 如果所有者的帐户密钥已泄露，攻击者就可以攻击其拥有的合约。 这就是采用基于角色的访问控制模式及多个管理帐户可能是更好方案的原因。

在基于角色的访问控制中，对敏感函数的访问分布在一组受信任的参与者之间。 例如，一个帐户可能负责铸造代币，而另一个帐户进行升级或暂停合约。 以这种方式分散访问控制，消除了单点故障并减少了对用户的信任假设。

##### 使用多重签名钱包

实施安全访问控制的另一种方法是使用`多重签名帐户`来管理合约。 与常规外部帐户不同，多重签名帐户由多个实体拥有，需要最低数量的帐户签名（比如 5 个中的 3 个）才能执行交易。

使用多重签名进行访问控制增加了额外一层安全性保障，因为需要多方同意才能对目标合约执行操作。 如果有必要使用所有权模式，这种方法尤其有用，因为攻击者或内部作恶者操控敏感的合约函数以达到恶毒目的会更加困难。

### 2. 使用 require()、assert() 和 revert() 语句保护合约操作 {#use-require-assert-revert}

如上所述，一旦智能合约部署到区块链上，任何人都可以调用其中的公共函数。 由于无法事先知道外部帐户将如何与合约交互，因此最好在部署之前实施内部安全措施以防出现有问题的操作。 可以通过使用 `require()`、`assert()` 和 `revert()` 语句强制执行智能合约中的正确行为，在执行不满足某些要求时触发异常并回滚状态变化。

**`require()`**：`require` 在函数开始时定义并确保在被调用函数执行之前满足预定义的条件。 `require` 语句可用于在处理函数之前验证用户输入、检查状态变量或验证调用帐户的身份。

**`assert()`**：`assert()` 用于检测内部错误，并检查代码中是否有违反“不变量”的情况。 不变量是关于合约状态的逻辑断言，对于函数的所有执行都应该为真。 举个例子，代币合约的最大总供应量或余额就是一个不变量。 使用 `assert()` 可确保合约永远不会达到易受攻击的状态，如果达到，对状态变量的所有更改都会回滚。

**`revert()`**：`revert()` 可用于 if-else 语句，可在要求的条件未满足时触发异常。 下面的示例合约使用 `revert()` 来保护函数的执行：

```
pragma solidity ^0.8.4;

contract VendingMachine {
    address owner;
    error Unauthorized();
    function buy(uint amount) public payable {
        if (amount > msg.value / 2 ether)
            revert("Not enough Ether provided.");
        // Perform the purchase.
    }
    function withdraw() public {
        if (msg.sender != owner)
            revert Unauthorized();

        payable(msg.sender).transfer(address(this).balance);
    }
}
```

### 3. 测试智能合约并验证代码正确性 {#test-smart-contracts-and-verify-code-correctness}

鉴于在[EVM虚拟机](/docs/mapo-stack/compatible-evm/index.md)中运行的代码的不可变性，智能合约在开发阶段需要更高水平的质量评估。 对合约进行大量测试并观察是否存在任何意外结果，将显著增强合约的安全性并为用户提供长远保护。

常用办法编写小单元测试，这些测试使用预计合约从用户处接收的模拟数据。 [单元测试](/docs/mapo-stack/compatible-evm/testing.md#单元测试)能够测试某些函数的功能并确保智能合约按预期运行。

遗憾的是，单独使用单元测试对提高智能合约的安全性效果甚微。 单元测试也许可以证明函数对于模拟数据正确执行，但单元测试的有效性受限于编写的测试。 这就意味着很难检测到威胁智能合约安全性的边缘情况和漏洞。

更好的方法是将单元测试与基于属性的测试相结合，后者是通过[静态和动态分析](/docs/mapo-stack/compatible-evm/testing.md#2-静态分析和动态分析-static-dynamic-analysis)进行的。 静态分析依赖于底层的表示（例如[控制流程图](https://en.wikipedia.org/wiki/Control-flow_graph)和[抽象语法树](https://deepsource.io/glossary/ast/)）分析可达到的程序状态和执行路径。 相比之下，动态分析技术（例如模糊测试）用随机输入值执行合约代码，以检测违反安全属性的操作。

[形式化验证](/docs/mapo-stack/compatible-evm/formal-verification.md)是另一项验证智能合约安全属性的技术。 与常规测试不同，形式化验证能够确证智能合约中没有错误。 这是通过制定细致描述安全属性的形式化规范并证明智能合约的形式化模型符合这一规范来实现的。

### 4. 申请代码独立审核 {#get-independent-code-reviews}

在测试智能合约后，最好请其他人检查源代码是否存在安全问题。 虽然测试无法发现智能合约中的所有缺陷，但进行独立审核能增加发现漏洞的可能性。

#### 审计 {#audits}

进行独立代码审核的方式之一是委托执行智能合约审计。 审计员是确保智能合约安全、没有质量缺陷和设计错误的关键所在。

#### 漏洞奖励 {#bug-bounties}

执行外部代码审查的另一种方法是设立漏洞奖励计划。 漏洞奖励是一种经济奖励，提供给发现应用程序中漏洞的个人（通常是白帽黑客）。

### 5. 智能合约开发过程中遵循最佳做法 {#follow-smart-contract-development-best-practices}

即使审计和漏洞奖励存在，你也有责任编写高质量的代码。 遵循正确的设计和开发流程是良好的智能合约安全性的开端：

- 将所有代码存放在一个版本控制系统中，例如 git

- 所有代码修改通过拉取请求进行

- 确保拉取请求至少有一位独立审核者 — 如果只有你一人完成项目，考虑和其他开发者相互进行代码审核

- 在开发环境下测试、编译、和部署智能合约

- 在如 Mythril 和 Slither 等基本代码分析工具中运行代码。 理想情况下，应在合并每个拉取请求前进行这一操作，并比较输出中的不同之处

- 确保代码在编译时没有错误，并且 Solidity 编译器没有发出警告

- 正确记录代码（使用 [NatSpec](https://solidity.readthedocs.io/en/develop/natspec-format.html)），并用易于理解的语言描述合约架构的细节。 这将使其他人更容易审计和审核你的代码。

### 6. 实施可靠的灾难恢复计划 {#implement-disaster-recovery-plans}

设计安全的访问控制、使用函数修饰符以及其他建议能够提高智能合约的安全性，但这些并不能排除恶意利用的可能性。 构建安全的智能合约需要“做好失败准备”，并制定好应变计划有效地应对攻击。 适当的灾难恢复计划应包括以下部分或全部内容：

#### 合约升级 {#contract-upgrades}

虽然智能合约默认是不可变的，但通过使用升级模式可以实现一定程度的可变性。 如果重大缺陷导致合约不可用并且部署新逻辑是最可行的选择，有必要升级合约。

合约升级机制的原理有所不同，但“代理模式”是智能合约升级最常见的方法之一。 代理模式将应用程序的状态和逻辑拆分到*两个*合约中。 第一个合约（称为“代理合约”）存储状态变量（如用户余额），而第二个合约（称为"逻辑合约"）存放执行合约函数的代码。

帐户与代理合约互动，代理合约通过[`delegatecall()`](https://docs.soliditylang.org/en/v0.8.16/introduction-to-smart-contracts.html?highlight=delegatecall#delegatecall-callcode-and-libraries)的低级调用将所有功能调用分发给逻辑合约。 与普通的消息调用不同，`delegatecall()` 确保在逻辑的合约地址上运行的代码是在调用合约的语境下执行。 这意味着逻辑合约将始终写入代理的存储空间（而非自身存储空间），并且 `msg.sender` 和 `msg.value` 的原始值保持不变。

将调用委托给逻辑合约需要将其地址存储在代理合约的存储空间。 因此，升级合约的逻辑就相当于部署另一个逻辑合约并在代理合约中存储新的地址。 由于对代理合约的后续调用会自动传送到新的逻辑合约，因此你“升级”了合约，但实际上并未修改代码。


#### 紧急停止 {#emergency-stops}

如上所述，大量审计和测试不可能发现智能合约中的所有漏洞。 无法修补在部署后出现的代码漏洞，因为你无法更改运行在智能合约中的代码。 而且，升级机制（如代理模式）可能需要时间来实现（它们往往需要多方批准），这只会给攻击者更多的时间来造成更大的破坏。

这种情况下，核心方案是实施一种“紧急停止”功能，阻止对合约中有漏洞的函数的调用。 紧急停止通常由以下几部分组成：

1. 表明智能合约是否处在停止状态的全局布尔变量。 在设置合约时该变量设为 `false`，但在合约停止后将回滚为 `true`。

2. 执行过程中引用该布尔变量的函数。 此类函数在智能合约没有停止时可以访问，而当紧急停止功能触发后则无法访问。

3. 可以访问紧急停止功能的实体，可将布尔变量设置为 `true`。 为防止恶意行为，对此功能的调用可以限制给一个可信地址（如合约所有者）。

一旦合约操作触发紧急停止，某些函数将无法调用。 这是通过把一些函数包装在引用该全局变量的修饰符中实现的。 以下[示例](https://github.com/fravoll/solidity-patterns/blob/master/EmergencyStop/EmergencyStop.sol)描述了该模式在合约中的实现：

```solidity
// 本代码未经专业审计，对安全性和正确性不做任何承诺。 如需使用，风险自负。

contract EmergencyStop {

    bool isStopped = false;

    modifier stoppedInEmergency {
        require(!isStopped);
        _;
    }

    modifier onlyWhenStopped {
        require(isStopped);
        _;
    }

    modifier onlyAuthorized {
        // Check for authorization of msg.sender here
        _;
    }

    function stopContract() public onlyAuthorized {
        isStopped = true;
    }

    function resumeContract() public onlyAuthorized {
        isStopped = false;
    }

    function deposit() public payable stoppedInEmergency {
        // Deposit logic happening here
    }

    function emergencyWithdraw() public onlyWhenStopped {
        // Emergency withdraw happening here
    }
}
```

以上示例展示了紧急停止的基本特点：

- 布尔值 `isStopped` 开始时求值为 `false`，但当合约进入紧急模式时求值为 `true`。

- 函数修饰符 `onlyWhenStopped` 和 `stoppedInEmergency` 检查 `isStopped` 变量。 `stoppedInEmergency` 用于控制在合约有漏洞时应该无法访问的函数（如 `deposit()`）。 对这些函数的调用将仅仅进行回滚而已。

`onlyWhenStopped` 用于在紧急情况下应该可调用的函数（如 `emergencyWithdraw()`）。 此类函数可以帮助解决问题，因此它们不在“受限制函数”之列。

紧急停止功能的应用，为处理智能合约中的严重漏洞提供了一种有效的权宜之计。 然而，这也意味着用户更需要相信开发者不会为自身利益激活这一功能。 为此，将紧急停止的控制权去中心化，使其受到链上投票机制、时间锁的约束或者需要来自多重签名钱包的批准，都是潜在的解决方案。

#### 事件监测 {#event-monitoring}

[事件](https://docs.soliditylang.org/en/v0.8.15/contracts.html#events)允许用户跟踪对智能合约函数的调用并监测状态变量的变化。 最理想的做法是将智能合约编写为能够在某一方采取对安全至关重要的操作（如提取资金）时发出一个事件。

记录事件并进行链下监测，可以深入了解合同的运作情况，有助于更快地发现恶意行为。 这意味着你的团队可以更快地应对黑客攻击并采取行动减轻对用户的影响，如暂停函数或进行升级。

你也可以选择一种现成的监测工具，只要有人与你的合约交互，就会自动转发警报。 这些工具将允许你根据不同的触发器创建自定义警报，如交易量、函数调用的频率或相关具体函数。 例如，你可以编写一个警报，当单笔交易的提款金额超过特定阈值时触发。

### 7. 设计安全的治理系统 {#design-secure-governance-systems}

你可能想要通过将核心智能合约的控制权转交给社区成员来去中心化你的应用。 在这种情况下，智能合约系统将包括一个治理模块 — 一种允许社区成员通过链上治理系统批准管理行为的机制。 例如，将代理合约升级为新实现的提案可能由代币持有人投票。

去中心化治理可能是有益的，特别是因为它符合开发者和最终用户的利益。 然而如果实现不当，智能合约治理机制可能会带来新的风险。 一种可能的场景是，攻击者通过取得`闪电贷`获得了很大的投票权（以持有的代币数量衡量）并通过一条恶意提案。

防止与链上治理有关的问题的一种方法是[使用时间锁](https://blog.openzeppelin.com/protect-your-users-with-smart-contract-timelocks/)。 时间锁阻止智能合约执行某些操作，直到经过特定的时间长度。 其他策略包括根据每个代币锁定的时间长短为其分配“投票权重”，或者检测一个地址在历史时期（例如，过去的 2-3 个区块）而不是当前区块的投票权。 这两种方法都减少了快速累积投票权以影响链上投票的可能性。

更多关于[设计安全的治理系统](https://blog.openzeppelin.com/smart-contract-security-guidelines-4-strategies-for-safer-governance-systems/)和[去中心化自治组织中的不同投票机制](https://hackernoon.com/governance-is-the-holy-grail-for-daos)的信息。

### 8. 将代码的复杂性降到最低 {#reduce-code-complexity}

传统的软件开发者熟悉 KISS（“保持简单、保持愚蠢”）原则，该原则建议不要将不必要的复杂性带入到软件设计中。 这与长期以来的见解“复杂的系统有着复杂的失败方式”不谋而合，而且复杂系统更容易出现代价高昂的错误。

编写智能合约时简洁化尤其重要，因为智能合约有可能控制大量的价值。 实现简洁化的一个窍门是，编写智能合约时在允许的情况下重用已存在的库，例如 [OpenZeppelin Contracts](https://docs.openzeppelin.com/contracts/4.x/)。 因为开发者对这些库已经进行了广泛的审计和测试，使用它们会减少从零开始编写新功能时引入漏洞的几率。

另一个常见的建议是通过将业务逻辑拆分到多个合约中，编写小型函数并保持合约模块化。 编写更简单的代码不仅仅会减少智能合约中的攻击面，还让推理整个系统的正确性并及早发现可能的设计错误变得更加容易。

### 9. 防范常见的智能合约漏洞 {#mitigate-common-smart-contract-vulnerabilities}

#### 重入攻击 {#reentrancy}

EVM虚拟机不允许并发，这意味着消息调用中涉及的两个合约不能同时运行。 外部调用暂停调用合约的执行和内存，直到调用返回，此时执行正常进行。 该过程可以正式描述为将[控制流](https://www.computerhope.com/jargon/c/contflow.htm)转向另一个合约。

尽管这种转向大多数情况下没有危害，但将控制流转向不受信任的合约可能引起问题，例如重入攻击。 当恶意合约在初始函数调用完成之前回调有漏洞的合约时，就会发生重入攻击。 这类攻击最好用一个例子来解释。

考虑一个简单的智能合约（“Victim”），它允许任何人存入和提取MAPO币：

```solidity
// This contract is vulnerable. Do not use in production

contract Victim {
    mapping (address => uint256) public balances;

    function deposit() external payable {
        balances[msg.sender] += msg.value;
    }

    function withdraw() external {
        uint256 amount = balances[msg.sender];
        (bool success, ) = msg.sender.call.value(amount)("");
        require(success);
        balances[msg.sender] = 0;
    }
}
```

该合约公开了 `withdraw()` 函数，允许用户提取先前存入合约的MAPO币。 当处理提款时，合约执行以下操作：

1. 检查用户的MAPO币余额
2. 将资金发送给调用地址
3. 将其余额重置为 0，防止用户再提取

如果从外部帐户调用 `withdraw()`，该函数将按预期执行：`msg.sender.call.value()` 向调用方发送MAPO币。 然而，如果 `msg.sender` 是智能合约帐户调用 `withdraw()`，使用 `msg.sender.call.value()` 发送资金还将使存储在该地址的代码运行。

假设以下是部署在合约地址的代码：

```solidity
 contract Attacker {
    function beginAttack() external payable {
        Victim(victim_address).deposit.value(1 mapo)();
        Victim(victim_address).withdraw();
    }

    function() external payable {
        if (gasleft() > 40000) {
            Victim(victim_address).withdraw();
        }
    }
}
```

此合约执行下面三项操作：

1. 接受来自另一帐户（如攻击者的外部帐户）的存款
2. 将 1 个MAPO币存入 Victim 合约
3. 提取存储在该智能合约中的 1 个MAPO币

这里没有什么问题，只是 `Attacker` 有另一个函数，如果传入的 `msg.sender.call.value` 调用剩余的燃料超过 40000，它就再次调用 `Victim` 中的 `withdraw()` 函数。 这使得 `Attacker` 能够重入 `Victim` 合约并在第一次调用 `withdraw` 函数结束*之前*提取更多资金。 这个循环如下所示：

```solidity
- Attacker 的外部帐户使用 1 个MAPO币调用 `Attacker.beginAttack()`
- `Attacker.beginAttack()` 将 1 个MAPO币存入 `Victim`
- `Attacker` 调用`Victim` 中的`withdraw()
- `Victim` 检查 `Attacker` 的余额（1 个MAPO币）
- `Victim` 发送 1 个MAPO币给 `Attacker`（触发默认函数）
- `Attacker` 再次调用 `Victim.withdraw()`（注意 `Victim` 并没有减少 `Attacker` 第一次提款后的余额）
- `Victim` 检查 `Attacker` 的余额（仍然是 1 个MAPO币，因为它没有应用第一次调用的效果）
- `Victim` 发送 1 个MAPO币给 `Attacker`（触发默认函数，让 `Attacker` 可以重入 `withdraw` 函数）
- 这个过程重复进行，直到 `Attacker `耗燃料，此时 `msg.sender.call.value `返回但不会触发额外的提款
- 最后 `Victim` 将第一笔交易（和后续交易）的结果应用于其状态，所以 `Attacker` 的余额被设置为 0
```

总结起来就是，由于调用者的余额在函数执行完成之前没有设置为 0，所以后续的调用会成功，让调用者可以多次提取他们的余额。 这种攻击可以用来提空智能合约中的资金, 正如[公开的重入攻击列表](https://github.com/pcaversaccio/reentrancy-attacks)所示，当前重入攻击仍是智能合约所面临的一个严重问题。

##### 如何防止重入攻击

应对重入攻击一种方法是遵循[检查-效果-交互模式](https://docs.soliditylang.org/en/develop/security-considerations.html#use-the-checks-effects-interactions-pattern)。 这种模式要求按照以下方式执行函数：最先执行在继续执行函数前执行必要检查的代码，再执行操作合约状态的代码，最后执行与其他合约或外部帐户交互的代码。

检查-效果-交互模式在 `Victim` 合约的修订版中采用，如下所示：

```solidity
contract NoLongerAVictim {
    function withdraw() external {
        uint256 amount = balances[msg.sender];
        balances[msg.sender] = 0;
        (bool success, ) = msg.sender.call.value(amount)("");
        require(success);
    }
}
```

该合约对用户的余额执行*检查*，应用 `withdraw()` 函数的*效果*（将用户的余额重置为 0）并继续执行*交互*（将MAPO币发送到用户的地址）。 这确保了合约在外部调用之前更新其存储空间，消除了导致第一次攻击的重入攻击的条件。 `Attacker` 合约可能仍然可以回调 `NoLongerAVictim`，但由于 `balances[msg.sender]` 已设置为 0，额外的提取将引发错误。

另一种方案是使用互斥锁（通常称为“mutex”），它锁定一部分合约状态直到函数调用完成。 互斥锁是通过布尔变量实现的，该变量在函数执行之前设置为 `true`，在调用完成后回滚为 `false`。 如下面的例子所示，使用互斥锁可以防止函数在初始调用仍在进行时不受到递归调用，从而有效地阻止重入攻击。

```solidity
pragma solidity ^0.7.0;

contract MutexPattern {
    bool locked = false;
    mapping(address => uint256) public balances;

    modifier noReentrancy() {
        require(!locked, "Blocked from reentrancy.");
        locked = true;
        _;
        locked = false;
    }
    // This function is protected by a mutex, so reentrant calls from within `msg.sender.call` cannot call `withdraw` again.
    //  The `return` statement evaluates to `true` but still evaluates the `locked = false` statement in the modifier
    function withdraw(uint _amount) public payable noReentrancy returns(bool) {
        require(balances[msg.sender] >= _amount, "No balance to withdraw.");

        balances[msg.sender] -= _amount;
        bool (success, ) = msg.sender.call{value: _amount}("");
        require(success);

        return true;
    }
}
```

还可以使用[拉取支付](https://docs.openzeppelin.com/contracts/4.x/api/security#PullPayment) 系统，该系统要求用户从智能合约中提取资金，而不是使用将资金发送到帐户的“推送支付”系统。 这样就消除了意外触发未知地址中代码的可能性（还可以防止某些拒绝服务攻击）。

#### 整数下溢和溢出 {#integer-underflows-and-overflows}

当算术运算的结果超出可接受的值范围，导致其“滚动”到可表示的最小值，整数溢出发生。 例如，`uint8` 只能存储最大为 2^-1=255 的值。 算术运算的结果如果大于 `255`，即溢出并重置 `Uint` 为`0`，这类似于汽车里程表，一旦达到最大里程 (999999) 示数就重置为 0。

整数下溢发生的原因类似：算术运算的结果小于可接受的范围。 比方说，你尝试减少 `uint8` 中的一个`0`，结果将只会滚动到最大的可表示值 (`255`)。

整数溢出和下溢都会导致合约的状态变量出现意外变化，引发意外的执行。 以下例子说明了攻击者如何利用智能合约的算数溢出执行无效操作：

```
pragma solidity ^0.7.6;

// This contract is designed to act as a time vault.
//用户可以向合约里存款但至少在一周内无法提款。
//用户还可以延长 1 周的等待期。

/*
1. 部署 TimeLock
2. 使用 TimeLock 地址部署 Attack
3. 调用 Attack.attack，发送 1 个MAPO币。 你将能够立即
   提取你的MAPO币。

发生了什么？
攻击造成了 TimeLock 溢出，并且能够在 1 周的等待期之
前提款。
*/

contract TimeLock {
    mapping(address => uint) public balances;
    mapping(address => uint) public lockTime;

    function deposit() external payable {
        balances[msg.sender] += msg.value;
        lockTime[msg.sender] = block.timestamp + 1 weeks;
    }

    function increaseLockTime(uint _secondsToIncrease) public {
        lockTime[msg.sender] += _secondsToIncrease;
    }

    function withdraw() public {
        require(balances[msg.sender] > 0, "Insufficient funds");
        require(block.timestamp > lockTime[msg.sender], "Lock time not expired");

        uint amount = balances[msg.sender];
        balances[msg.sender] = 0;

        (bool sent, ) = msg.sender.call{value: amount}("");
        require(sent, "Failed to send Ether");
    }
}

contract Attack {
    TimeLock timeLock;

    constructor(TimeLock _timeLock) {
        timeLock = TimeLock(_timeLock);
    }

    fallback() external payable {}

    function attack() public payable {
        timeLock.deposit{value: msg.value}();
        /*
        if t = current lock time then we need to find x such that
        x + t = 2**256 = 0
        so x = -t
        2**256 = type(uint).max + 1
        so x = type(uint).max + 1 - t
        */
        timeLock.increaseLockTime(
            type(uint).max + 1 - timeLock.lockTime(address(this))
        );
        timeLock.withdraw();
    }
}
```

##### 如何防止整数溢出和下溢

从 0.8.0 版开始，Solidity 编译器禁用导致整数下溢和溢出的代码。 然而，用较低编译器版本编译的合约应当对涉及算术运算的函数执行检查，或者使用检查是否发生下溢/溢出的库（例如 [SafeMath](https://docs.openzeppelin.com/contracts/2.x/api/math)）。



## 相关教程 {#related-tutorials}

- **[Fork Checker](https://forkchecker.hashex.org/)** - _免费的在线工具，用于检查所有关于分叉合同的现有信息。_
- **[ABI 编码器](https://abi.hashex.org/)** - _免费在线服务，用于编码您的 Solidity 合约函数和构造函数参数。_
- **[OpenZeppelin Defender Sentinels](https://docs.openzeppelin.com/defender/sentinel)** - _一种用于自动监测和响应智能合约中事件、函数和交易参数的工具。_
- **[Tenderly Real-Time Alerting](https://tenderly.co/alerting/)** - _一种在智能合约或钱包发生异常或意外事件时，为你获取实时通知的工具。_
- **[Awesome BlockSec CTF](https://github.com/blockthreat/blocksec-ctfs)** - _精选区块链安全攻防、实战、[夺旗](https://www.webopedia.com/definitions/ctf-event/amp/)竞赛和解决方案的文章列表。_
- **[Damn Vulnerable DeFi](https://www.damnvulnerabledefi.xyz/)** - _通过实战演练学习去中心化金融智能合约的攻击性安全，并培养漏洞搜查和安全审计方面的技能。_
  