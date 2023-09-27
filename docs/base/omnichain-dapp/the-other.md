# 第三方信任跨链与点对点跨链方案区别

MAPO Protocol is the only infrastructure made for omnichain dApps that connects all chains, provides security finality, charges the lowest cross-chain gas fees, and also offers complete dApp development service toolkits. 

![](acomp.png)


## MAPO Protocol VS Cosmos & Polkadot

MAPO protocol shares lots of similar features of Polkadot and Cosmos, i.e. *Interoperate with other chains through relay chain*, *Verification Finality of cross-chain assets and data through Light-clients*, *Cross-chain interoperability is achieved through modules including light-client, cross-chain messaging system, and cross-chain assets and data management*.

![](cosmospolkadot_1.png)

![](cosmospolkadot_2.png)


## MAPO Protocol VS cross-chain solutions without relay chain

### Comparisons between MAPO Protocol and other lightweight cross-chain solutions with no relay chain

![](comp2.png)

- The above lightweight solutions, including MAPO Protocol, achieved cross-chain functionality by deploying cross-chain communication components on-chain or inter-chain, which are better solutions than Cosmos & Polkadot (require embedding SDK to the bottom layer of the blockchain);
- MPC (Multi-Party Computation) technology is easy to develop but has obvious security risks. 
- Using Oracle to conduct cross-chain verification is ambiguity , and the risk of colluding with messengers always exists. 
- Using Light-client to conduct cross-chain verification is the ultimate security mechanism adopted in MAPO Protocol, Cosmos, and Polkadot. 
- The Light-client verification mechanism will lead to massive consumption of gas fees for cross-chain activities between heterogeneous chains. The better way is to use a relay chain, compatible with multiple chains' algorithm, thus enabling a homogeneous cross-chain and forming the ultimate cross-chain verification network. MAPO Protocol has developed the MAPO Relay Chain as such. 
- As the cross-chain asset and data assembly, MAPO Relay Chain supports the native deployment of omnichain dApps, including cross-chain bridge applications,  differentiating MAPO Protocol from all the other competitors.
