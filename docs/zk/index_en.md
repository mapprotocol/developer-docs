---
title: Zero-Knowledge Proof
description: MAP Protocol uses ZK to refactor light clients verification network.
lang: en
---
# Zero-Knowledge Proof

## What is Zero-Knowledge Proof?
First appeared in a 1985 paper — [“The knowledge complexity of interactive proof system”](https://people.csail.mit.edu/silvio/Selected%20Scientific%20Papers/Proof%20Systems/The_Knowledge_Complexity_Of_Interactive_Proof_Systems.pdf) —  Zero-knowledge proofs are a cryptographic protocol that allows one party (the prover) to prove to another party (the verifier) that a given statement is true, without revealing any information beyond the validity of the statement itself. This means that the verifier learns nothing about the statement or the information that substantiates it, apart from the fact that it is indeed true.

It operates on the principle of [challenge-response protocols](https://csrc.nist.gov/glossary/term/challenge_response_protocol). The prover makes an assertion and provides evidence to the verifier without disclosing any critical information. The verifier then issues challenges to the prover, who must respond in a way that convinces the verifier of the truth of the assertion. This process is repeated multiple times to ensure that the proof is accurate and not a result of chance. The security of zero-knowledge proofs relies on the computational difficulty of certain mathematical problems, ensuring that it's infeasible for an adversary to cheat the system.

From financial transactions and supply chains to identity verification, zero-knowledge proofs have a wide array of applications in various sectors. Beyond those common use cases, zk-proof can also be used for improving network verification efficiency. MAP Protocol has refactored its light clients verification network with zk-proof.

## ZK-Improved Light Clients for MAP Relay Chain
The cross-chain verification of MAP Protocol is mainly performed by the light client smart contract of the origin chain deployed on the target chain to perform the following two verifications:
1. **Correctness verification of the block header**: Verify the legality of the block header requested by the maintainer to be written in. Depending on the different chain consensus mechanisms, this verification scheme will vary. For chains using PoS and BFT mechanisms, it is usually to verify that the legal signature represented in the block header has more than 2/3 of the voting rights.
2. **Verification of Merkle proof**: Verify that a specific event is emitted at a specific block height, the correct Merkle root value is required in the block header, and the first step ensures correctness. In a group of blockchains similar to Ethereum's structure, this Merkle proof is usually proof of the existence of the Merkle Patricia Trie, that is, the receipt tree MPT does indeed contain a specific event.

Improving the implementation of the MAP Relay Chain light client using zkSNARK technology aims to solve two problems:
1. Reduce the amount of MAP Relay Chain metadata that needs to be stored in the light client contract, lowering the gas cost for updating the state of the light client itself.
2.  Move the signature validity and signature weight check part of the block header legality verification process into the zero-knowledge proof circuit and use the Groth16 scheme for verification to reduce gas costs.

In the MAP Relay Chain light client smart contract, it is necessary to store the public keys and staking weight information of all validators in the current epoch. When verifying the validity of a new block header, based on the information in the block header and the current validator information stored in the light client contract, the light client contract can calculate the aggregated public key required for the aggregate signature verification of the block header. 

If the aggregate signature verification passes and the sum of the voting weights represented by the aggregate public key exceeds 2/3, the block header will pass verification. In the MAP Relay Chain light client built on zkSNARK, only the commit value of the current validator set metadata needs to be stored, i.e., SHA256((pk_0, wt_0), (pk_1, wt_1), ..., (pk_n, wt_n)). This means the light client contract is simplified from needing to store n public keys and weight information to only needing to store a 256-bit commit value.

Therefore, the block header verification logic of the MAP Relay Chain light client built on zkSNARK can be simplified as whether the input block header is the correct block within the validator set information represented by the current commit value. The success or failure of this judgment depends on the zkSNARK proof input along with the block header.

### Cross-chain mechanism after introducing zkSNARK
For clarity, here are the newly introduced roles of the prover in the cross-chain process after introducing zkSNARK, the updated logic of messenger/maintainer, and the inputs received by each party:
![LCrefactored](docs/zk/LCrefactored.png)

Prover's input: block header, public key of each validator in the current validator set, and their voting weights.
1. Circuit's input: t0 and t1, the aggregate signature to be verified, public keys and voting weights of each validator in the current validator set, and the bitmap indicating the aggregate validators.
2. Input for light client event legitimacy verification: block header, zk-proof, and mpt-proof.
3. Input for light client sync commit legitimacy verification: block header, zk-proof.

Where t0 and t1 are the intermediate values of block header hashToG1, referring to [the current light client implementation code](https://github.com/mapprotocol/map-contracts/blob/main/mapclients/eth/contracts/bls/BGLS.sol#L204-L218). The reason why the circuit chooses t0 and t1 as inputs instead of the entire block header is that expressing the hashToBase calculation inside hashToG1 with a circuit is costly and not worth it. When the prover server gets the block header information submitted by the maintainer or messenger, it continues to execute related operations.

The prover can be understood as a server that generates proofs, exposing an interface for proof generation requests, with input parameters: block header, public key of each validator in the current validator set, and their voting weights. The output is zk-proof. 

For the aforementioned reasons, before the prover starts calculating the zk-proof, it first extracts and calculates the parameters required to generate the zk-proof from the block header in the request: t0 and t1, and the bitmap indicating the validators participating in the aggregate signature, then performs the specific calculation process of zk-proof.

Circuit’s encoded logic is as follows:
1. According to the bitmap in the block header and the information of the full set of validators, calculate the aggregate public key and verify that the signature weight represented by the aggregate public key exceeds 2/3.
2. Calculate the final result of hashToG1 based on t0 and t1, use this value, the aggregate public key to be verified, and the aggregate BLS verification to check the legitimacy of the signature.

The final zk-proof is an assertion of the legality of the above statement under specific public input. In the process of verifying the block header by the light client, public input is constructed based on the commit information stored by the light client and the block header information to be verified, and the legality of the zk-proof is verified. If it can be verified, it means that the zk-proof is valid, thereby proving the legitimacy of the corresponding block header.
### Example Walkthrough
Taking the light client's verification of event legality as an example, its inputs include the block header, zk-proof, and merkle-proof. The entire proof verification process is as follows:

1. Calculate t0 and t1 based on the input block header and hashToBase.
2. Take t0, t1, and the commit of the current validator set stored by itself as public inputs, and verify the legality of zk-proof according to the groth-16 scheme.

If the second step is verified, it means the block header is legal. Then extract the root node (MPT root) of the Merkle Patricia Tree (MPT) from it and continue to verify the legality of the mpt-proof.

Step one is an additional calculation introduced to accommodate the construction of the circuit. The appropriate principle of using the appropriate scheme to verify the appropriate thing is reflected: the calculation involved in hashToBase is not costly with solidity, but it is not worth it with a circuit. The key step of block header verification is the second step, which actually verifies one thing: the input block header is a legitimate block header under the validator set corresponding to the commit stored by the light client. This inference can be easily deduced from the internal logic of the circuit. The third step has no difference from the current implementation of the light client.

The process of the light client verifying the legitimacy of the synchronization submission is basically consistent with the above process, with the only difference being that the operation of verifying the legality of the MPT root in the third step is replaced with recalculating the commit according to the new validator set information and updating its own storage.

##@ Conclusion
According to the foregoing discussion, after the MAP Relay Chain light client is upgraded based on zkSNARK, the corresponding adjustments that the maintainer/messenger needs to make are as follows:
1. Before the maintainer wants to update the light client state, they need to obtain zk-proof from the prover and construct a tx with the block header and zk-proof.
2. Before the messenger wants to initiate a cross-chain request, they also need to obtain zk-proof from the prover and construct a tx with the block header, zk-proof, and mpt-proof.

Users who interact cross-chain through the front end usually feel the changes happening behind the scenes. To highlight the characteristics of zero-knowledge proof, some information related to zero-knowledge proof can be displayed on the frontend:
1. When the messenger submits a zk-proof request, the frontend can simply display a notification prompt, such as: "A cross-chain zk-proof request has been submitted to the prover."
2. When the messenger obtains the zk-proof request, the front end can simply display a notification prompt, such as: "zk-proof has been obtained."
3. When the messenger submits a transaction containing the block header, zk-proof, and mpt-proof, the frontend prompts: "zk-proof and cross-chain request have been submitted to the target chain."

After the third step's transaction is packaged, the front end can prompt: "The zk-proof and cross-chain request have been processed by the target chain.
