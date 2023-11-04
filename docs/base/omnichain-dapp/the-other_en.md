# Differences Between Third-Party Trusted Cross-Chain and Peer-to-Peer Cross-Chain Solutions

There are many different cross-chain solutions available, but the fundamental difference lies in whether the cross-chain validity verification complies with Satoshi Nakamoto's definition of peer-to-peer. However, from a user experience perspective, more centralized solutions often mean more convenience. Nevertheless, for decentralized asset security, a peer-to-peer solution is the ultimate choice.

Due to irresponsible marketing, almost all cross-chain solutions claim to be decentralized. In reality, however, most solutions are centralized, meaning that privileged roles exist. There are many cross-chain solutions on the market, but they can generally be divided into the following three categories:

## Centralized Exchange-Based Cross-Chain

This involves using centralized exchanges for cross-chain asset exchange. The degree of compliance and regulation of centralized exchanges varies, and unregulated exchanges can pose significant asset security risks. Cross-chain services offered by centralized exchanges do not facilitate cross-chain interoperability between smart contracts.

## Trusted Third-Party Cross-Chain

In this type of cross-chain solution, a set of trusted third-party entities or validators is involved. These third parties are responsible for validating and confirming cross-chain transactions. While these solutions offer some level of decentralization compared to centralized exchanges, they still rely on trusted third parties, leading to some degree of centralization in governance.

### MPC (Multi-Party Computation) Multi-Signature Cross-Chain

This approach relies on a group of signatories who, as external witnesses, confirm the legitimacy of cross-chain transactions or smart contract interactions initiated on the source chain. Some MPC mechanisms may involve enhanced security measures, such as requiring signatories to stake assets. However, this may resemble the practices of centralized banking institutions.

### Oracle Witness Mechanism

The oracle mechanism involves a set of oracle nodes that read the block header information of the source chain, where the cross-chain transaction request originates. They then relay this information to the target chain, where the source chain's light client block header information is verified. This model relies entirely on external off-chain roles, rather than code trust.

### Relay Chain Node Committee Verification Mechanism
 
In this approach, validators on a relay chain verify cross-chain requests from the source chain for their legitimacy. Essentially, this model is similar to the MPC multi-signature mechanism. While it may seem that relay chain validators are confirming transactions, from the perspective of the source chain, these validators have no direct affiliation and are entirely independent third parties.


## Peer-to-Peer Cross-Chain with ZK (Zero-Knowledge) Light Clients and Smart Contracts

Peer-to-peer cross-chain mechanisms rely on code trust rather than trust in a specific group of third-party roles. While independent third-party roles are still required for message relay during cross-chain operations, the validity of cross-chain requests depends on the code trust provided by smart contracts. This may sound abstract but can be thoroughly understood by exploring the MAP Protocol whitepaper.

How Does Peer-to-Peer Cross-Chain Achieve Decentralized Code Trust?

First, whether it's a peer-to-peer or third-party trust-based cross-chain solution, the fundamental requirement is that cross-chain transactions are legitimate and verifiable on the source chain. This ensures that cross-chain requests are real and not fraudulent.

Second, all cross-chain solutions, whether peer-to-peer or third-party trust-based, require a set of off-chain message relay roles. This is because source and target chains are not part of a single distributed ledger and need a group of roles to facilitate cross-chain message relay.

Third, peer-to-peer cross-chain ensures that, on the target chain, there is complete code trust verification of the legitimacy of cross-chain requests, and this verification occurs without relying on third-party privileged roles. How is this achieved? It relies on deploying a smart contract on the source chain that acts as a trusted source of information and is responsible for verifying the legitimacy of cross-chain requests.

In summary, the most significant difference between peer-to-peer and third-party trust-based cross-chain solutions lies in whether independent smart contracts perform cross-chain verification independently. This is the field of lightweight client smart contracts in cross-chain technology. Constructing lightweight client smart contracts is a challenging task, as they need to be well-versed in various blockchain mechanisms and require a deep understanding of cryptography to build a low-gas, peer-to-peer verification network.