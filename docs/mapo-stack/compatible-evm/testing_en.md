---
title: Testing smart contracts
description: An overview of techniques and considerations for testing smart contracts.
lang: en
---

Testing smart contracts before [deploying](/docs/mapo-stack/compatible-evm/deploying_en.md) to Mainnet is a minimum requirement for [security](/docs/mapo-stack/compatible-evm/security_en.md). There are many techniques for testing contracts and evaluating code correctness; what you choose depends on your needs. Nevertheless, a test suite made up of different tools and approaches is ideal for catching both minor and major security flaws in contract code.

The following `MAPO-Relay-Chain` is collectively referred to as MAPO.

## what-is-smart-contract-testing

"Smart contract testing" refers to a comprehensive analysis and assessment of smart contracts to evaluate the quality of their source code during the development cycle. Testing smart contracts can help identify errors and vulnerabilities more easily, reducing the likelihood of software bugs and aiding in the prevention of costly exploits.

There are various forms of smart contract testing, each with its own benefits. Testing MAPO smart contracts can be categorized into two main strategies: **automated testing** and **manual testing**.

### automated-testing

Automated testing uses tools that automatically check a smart contracts code for errors in execution. The benefit of automated testing comes from using [scripts](https://www.techtarget.com/whatis/definition/script?amp=1) to guide the evaluation of contract functionalities. Scripted tests can be scheduled to run repeatedly with minimal human intervention, making automated testing more efficient than manual approaches to testing.

Automated testing is particularly useful when tests are repetitive and time-consuming; difficult to carry out manually; susceptible to human error; or involve evaluating critical contract functions. But automated testing tools can have drawbacks—they may miss certain bugs and produce many [false positives](https://www.contrastsecurity.com/glossary/false-positive). Hence, pairing automated testing with manual testing for smart contracts is ideal.


### manual-testing

Manual testing is human-aided and involves executing each test case in your test suite one after the other when analyzing a smart contracts correctness. This is unlike automated testing where you can simultaneously run multiple isolated tests on a contract and get a report showing all failing and passing tests.

Manual testing can be carried out by a single individual following a written test plan that covers different test scenarios. You could also have multiple individuals or groups interact with a smart contract over a specified period as part of manual testing. Testers will compare the actual behavior of the contract against the expected behavior, flagging any difference as a bug.

Effective manual testing requires considerable resources (skill, time, money, and effort), and it is possible—due to human error—to miss certain errors while executing tests. But manual testing can also be beneficial—for example, a human tester (e.g., an auditor) may use intuition to detect edge cases that an automated testing tool would miss.

## benefits-of-smart-contract-testing

Testing smart contracts is crucial for the following reasons:

### 1. smart-contracts-are-high-value-applications

Smart contracts often handle high-value financial assets, especially in industries like `decentralized finance (DeFi)` and valuable assets like `non-fungible tokens (NFTs)`. Therefore, small bugs in smart contracts can potentially lead to significant and irreversible losses for users. Comprehensive testing, however, can uncover errors in smart contract code and reduce security risks before deployment.

### 2. #smart-contracts-are-immutable

Smart contracts deployed in the [EVM](/docs/mapo-stack/compatible-evm/index_en.md) are, by default, immutable. While traditional developers might be accustomed to fixing software bugs after deployment, smart contracts have very little room for patching security vulnerabilities once they are running on the blockchain.

Although smart contracts can have upgrade mechanisms, such as proxy patterns, implementing these can be challenging. Upgrades, apart from reducing immutability and introducing complexity, often require complex governance processes.

In most cases, upgrades should be seen as a last resort and should be avoided unless absolutely necessary. Detecting potential vulnerabilities and defects in smart contracts during the pre-release phase can reduce the need for logic upgrades.

## automated-testing-for-smart-contracts

### 1. functional-testing

Functional testing verifies the functionality of a smart contract and ensures that every function in the code works as expected. Functional testing requires an understanding of how your smart contract behaves under specific conditions. You can then test each function by running computations with selected values and comparing the returned output to the expected output.

Functional testing encompasses three approaches: **unit testing**, **integration testing**, and **system testing**.

#### unit testing

Unit testing involves testing individual components of a smart contract in isolation to ensure their correctness. Unit tests are simple, fast to run, and can provide clear insights into what went wrong if a test fails.

Unit testing is crucial for smart contract development, especially when you need to add new logic to your code. You can verify the behavior of each function and confirm that it executes as expected.

Running unit tests typically involves creating assertions – simple, informal statements specifying the requirements of your smart contract. You can then use unit tests to check if each assertion holds true during execution.

Examples of assertions related to contracts include:

i. "Only administrators can pause the contract."

ii. "Non-administrators are not authorized to mint new tokens."

iii. "The contract will revert on error."

#### integration testing

In the testing hierarchy, integration testing is a level above unit testing. In integration testing, independent components of a smart contract are tested together.

This approach detects errors that result from interactions between different components of the contract or interactions across multiple contracts. If you have a complex contract with multiple functionalities or a contract that interacts with other contracts, you should use this method.

Integration testing helps ensure that features like [inheritance](https://docs.soliditylang.org/en/v0.8.12/contracts.html#inheritance) and dependency injection work correctly.

#### system testing

System testing is the final stage of smart contract functional testing. It evaluates the smart contract as a fully integrated product to see if it performs as specified in the technical requirements.

You can think of this stage as checking the end-to-end processes of the smart contract from a user's perspective. A good way to conduct system testing for smart contracts is to deploy them in an environment that resembles the production, such as a `test network` or a `development network`.

Here, end-users can conduct trial runs and report any issues with the contract's business logic and overall functionality. System testing is essential because once the contract is deployed to the MAPO mainnet environment, you cannot change the code.

### 2. static-dynamic-analysis

Static analysis and dynamic analysis are two automated testing methods for evaluating the security quality of smart contracts. However, these two techniques employ different approaches to discover defects in contract code.

#### static analysis

Static analysis examines the source code or bytecode of a smart contract before execution. This means you can debug contract code without actually running the program. Static analyzers can detect common vulnerabilities in smart contracts and help enforce best practices.

#### dynamic analysis

Dynamic analysis techniques require executing the smart contract in a runtime environment to identify issues in the code. Dynamic code analyzers observe the contract's behavior during execution and generate detailed reports on identified vulnerabilities and compliance violations.

Fuzz testing is an example of dynamic analysis used for testing contracts. During fuzz testing, a fuzzer provides your smart contract with improperly formatted and invalid data and monitors how the contract responds to these inputs.

Like any program, smart contracts rely on user-provided inputs to perform their functions. While we assume users will provide correct inputs, this is not always the case.

In some instances, sending incorrect input values to a smart contract may result in resource leaks, crashes, or, worse yet, unintended code execution. Fuzz testing activities preemptively identify such issues, allowing you to eliminate vulnerabilities.

## manual-testing-for-smart-contracts

### 1. code-audits


Code audit is a comprehensive evaluation of a smart contract's source code to discover potential points of failure, security vulnerabilities, and poor development practices. While code audits can be automated, we are referring here to manual, human-assisted code analysis.

Code auditing involves thinking from an attacker's perspective to map potential attack vectors within the smart contract. Even if you run automated audits, analyzing every line of source code is the minimum requirement for writing a secure smart contract.

You can also opt for a security audit to provide users with higher assurances of smart contract security. Audits benefit from extensive analyses performed by cybersecurity professionals, detecting potential vulnerabilities or errors that could compromise the smart contract's functionality.

### 2. bug-bounties

A bug bounty is an economic reward given to individuals who discover vulnerabilities or errors in program code and report them to developers. Bug bounties are similar to audits in that they involve enlisting the help of others to uncover flaws in a smart contract. The key difference is that bug bounty programs are open to a wider community of developers and ethical hackers.

Bug bounty programs often attract a large pool of ethical hackers and independent security professionals with unique skills and experiences. This can be an advantage compared to smart contract audits that primarily rely on teams with potentially limited or narrow expertise.

## testing-vs-formal-verification

While testing helps confirm that a contract returns the expected results for certain input data, it cannot ultimately prove that untested inputs also behave as intended. Testing a smart contract cannot guarantee "functional correctness," meaning it doesn't demonstrate that the program behaves as required for all input values and sets of conditions.

For this reason, we encourage developers to incorporate **formal verification** into their methods for assessing the correctness of smart contracts. Formal verification uses [formal methods](https://www.brookings.edu/techstream/formal-methods-as-a-path-toward-better-cybersecurity/) – rigorous mathematical techniques for specifying and verifying software.

Formal verification is considered essential for smart contracts because it helps developers formally test assumptions related to the contract. The approach involves creating a formal specification that describes the properties of the smart contract and verifying whether the formal model of the contract matches the specification. This method increases confidence that the smart contract will only execute the functions defined in its business logic and nothing else.

## testing-tools-and-libraries

### unit-testing-tools

**Solid-coverage** 

- [GitHub](https://github.com/sc-forks/solidity-coverage)

**Waffle** 

- [document](https://ethereum-waffle.readthedocs.io/en/latest/)
- [GitHub](https://github.com/TrueFiEng/Waffle)
- [web](https://getwaffle.io/)

**Remix testing** 

- [document](https://remix-ide.readthedocs.io/en/latest/unittesting.html)
- [GitHub](https://github.com/ethereum/remix-project/tree/master/libs/remix-tests)

**OpenZeppelin Test Helpers** 

- [GitHub](https://github.com/OpenZeppelin/openzeppelin-test-helpers)
- [document](https://docs.openzeppelin.com/test-helpers)

**Truffle 智能合约测试框架**

- [document](https://trufflesuite.com/docs/truffle/testing/testing-your-contracts/)
- [web](https://trufflesuite.com/)

**Brownie** 

- [document](https://eth-brownie.readthedocs.io/en/v1.0.0_a/tests.html)
- [GitHub](https://github.com/eth-brownie/brownie)

**Wok**

- [document](https://ackeeblockchain.com/woke/docs/latest/testing-framework/overview/)
- [Github](https://github.com/Ackee-Blockchain/woke)

### static-analysis-tools

**Mythril** 

- [GitHub](https://github.com/ConsenSys/mythril-classic)
- [document](https://mythril-classic.readthedocs.io/en/master/about.html)

**Slither** 

- [GitHub](https://github.com/crytic/slither)

**Rattle** 

- [GitHub](https://github.com/crytic/rattle)

### 动态分析工具 {#dynamic-analysis-tools}

**Echidna** 

- [Github](https://github.com/crytic/echidna/)

**Harvey** 

- [web](https://consensys.net/diligence/fuzzing/)

**Manticore** 

- [Github](https://github.com/trailofbits/manticore)
- [document](https://github.com/trailofbits/manticore/wiki)

