# 第三方信任跨链与点对点跨链方案区别

跨链的方案有很多种，根本的区别在于，跨链有效性验证是否符合中本聪点对点的定义。但从在使用体验上而已，更加中心化的方案往往意味着更便捷的操作，然而在资产去中心化安全上，点对点的方案才是最终的选择。

因为营销的不负责任，几乎所有的跨链方案都宣称自己是去中心化的，当然实际上，几乎所有的方案都是中心化的，即有特权角色存在的。市面上存在的跨链方案很多，但总体来分为以下三种：

## 中心化交易所跨链。

通过中心化交易所进行跨链资产兑换。这种方案依赖于中心化交易所的合规被监管程度，不被监管的交易所往往存在重大的资产安全问题。并且中心化交易所跨链服务，无法为开发者提供智能合约间的跨链互操作。


## 有特权角色跨链。

有特权角色的定义多种多样，我们不妨参照比特币白皮书中的定义。若一笔跨链交易的最终确认，是由一组第三方决定的，这就不符合中本聪点对点特性的定义。跨链方案因不对用户进行KYC，对于提币也没有中心化交易所一样的审核机制，所以成为黑客犯罪组织的乐园。

### MPC多签机制跨链。
 依赖一组签字人，以链外见证人的方式，对起源链上发起的跨链交易或智能合约互操作请求，进行合法性确认。 有些MPC机制是采用了加强安全模式，例如让签字人进行资产质押，但这与中心化银行机构的做法相同。

### Oracle见证人机制。
oracle机制采用一组oracle节点，在起源链上读取发起跨链交易请求的轻客户端区块头信息，发至目标链上，由目标链存储的起源链轻客户端区块头信息进行验证。该模式完全依靠相关链外角色，而非代码信任。

### 中继链节点委员会验证机制。
 中继链的验证者们，对起源链的跨链请求，进行合法性验证。该模式本质上与MPC多签机制是一样的，虽然看起来是中继链的验证者在进行确认，但对于起源链而言，这些验证者与起源链并无关联，是完全独立的第三方。



## 点对点跨链，ZK轻客户端智能合约。

点对点跨链机制的核心是代码信任，而非对任何一组第三方角色的信任。虽然在跨链过程中，依然需要独立第三方角色参与跨链消息转发，但跨链请求是否有效，并非依赖这批角色，而是依赖智能合约带来的代码信任。这听起来会特别抽象，但是通过以下的解释，以及深入阅读MAP Protocol白皮书，会彻底理解这种机制。

点对点跨链验证如何做到去中心化代码信任？

首先，无论点对点还是第三方信任跨链，核心的诉求是该跨链交易在发起跨链请求的起源链上是合法的，真实的，而不是在起源链并没有足额的跨链资金，虚假伪造起源链的假金额，让跨链网络执行，在目标链上取出资产，实现黑客攻击。

第二，任何的跨链方案，无论点对点还是第三方特权角色信任，都需要一组链间消息传递角色。这是因为起源链和目标链并非一套分布式账本，需要一组角色进行链间消息传递。

第三，点对点跨链可以做到在目标链完全无特权角色、基于代码信任验证起源链交易请求的合法性。这是如何做到的呢？机制是非常简单的，以PoS机制公链为例，由链间消息传递角色，将起源链每一届签名委员会（每一届签名委员会负责该届委员会任期内的链上交易打包上链确认账本）的签名信息（签名公钥和签名权重），转发写进目标链上ZK轻客户端智能合约。当有跨链交易请求发生时，目标链上部署的起源链ZK轻客户端智能合约，对该笔交易在起源链上的相关验证者签名信息和区块信息，进行验证（校对是否符合真实的起源链验证签名委员会信息，这就如同校对银行签名一样）。那会不会有可能链间消息传递角色传过来的起源链签名委员会信息是虚假的呢？只要是链外第三方角色，虚假的可能性是肯定存在的，那如何规避呢？这就是代码信任和区块链技术的力量了。

第四，ZK轻客户端智能合约如何做到跨链独立自验证，完全代码信任，无第三方特权角色？

ZK零知识证明技术的加入让整个跨链验证智能合约的GAS成本降低数倍，然而单纯ZK是无法解决独立自验证的问题。上面提到，如何让链间消息传递角色传递过来的虚假起源链签名委员会信息无效呢？这就依靠在起源链上部署一个完全代码信任的智能合约，这个智能合约的职责是存储起源链每届签名委员会信息，并且负责具体跨链请求合法性的验证。众所周知，PoS机制签名委员会是由上届三分之二的委员投票签名产生的，以此形成了一个可溯源的授权链。所以虚假的起源链验证委员会信息，并无上届委员会的合法签名，而上届委员会的签名，是存储在目标链智能合约中的，这是可溯源的没法篡改的。所以虚假的信息，是无法通过验证的。



总体而言，点对点跨链和第三方特权角色跨链的技术实现上，最大的差异点在于，是否依靠智能合约独立进行跨链验证。这就是跨链技术领域中的 轻客户端智能合约。轻客户端智能合约的构建是智能合约中的天花板之一。他需要对区块链的各种机制了如指掌，并且为了构建低gas费点对点验证网络，还需要对密码学非常精通。
