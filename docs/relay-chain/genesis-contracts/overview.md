---
title: Atlas Genesis contracts
description:
lang: en
---


The genesis contracts of `Atlas` are a set of smart contracts that include several key functional contracts, which
collaborate to support the operation of the `Atlas` network.

+ `Accounts`: The Accounts contract is used to manage the accounts of validators and voters on the `Atlas` network. It
  primarily includes functionalities for creating new accounts, setting and viewing account information, account
  authorization, and verification. The Accounts contract is associated with the Election and LockedGold contracts,
  allowing users to participate in validator elections using locked MAPO tokens.

+ `Election`: The Election contract is responsible for managing the validator elections on the `Atlas` network. It defines
  the election algorithm and conditions for validators and manages and maintains various voting information states. The
  Election contract utilizes the locked MAPO tokens in the LockedGold contract as collateral for validators. Validators
  maintain network security and stability by participating in validation and transaction packaging.

+ `EpochRewards`: The EpochRewards contract manages and distributes rewards to validators and other network participants
  in the `Atlas` network. It distributes a certain amount of MAPO tokens as rewards to eligible validators based on
  their contributions and the network's security performance. The EpochRewards contract is closely related to the
  Validators and LockedGold contracts, as validators need to register in the Validators contract to participate in
  reward distribution.

+ `Validators`: The Validators contract maintains and manages the validator nodes in the `Atlas` network. It defines the
  roles and responsibilities of validators and provides necessary functionalities and interfaces for validators to
  validate and package transactions and participate in the consensus process. The Validators contract works together
  with the Election contract to determine which validators are eligible to participate in block validation and
  generation. The Validators contract is also related to the LockedGold contract, as validators need to lock a certain
  amount of MAPO tokens in the LockedGold contract as collateral.

+ `LockedGold`: The LockedGold contract is used to manage the locked assets on the `Atlas` network. Users can lock their
  MAPO tokens in the contract as collateral for validators or participate in network governance. Locking assets helps
  ensure network security and enables participation in decision-making voting in `Atlas`. The LockedGold contract is
  directly related to the Election and Validators contracts, as validators need to lock a certain amount of MAPO tokens
  in the LockedGold contract and use these locked assets to participate in validator elections and validation processes.

These genesis contracts are closely interconnected and together constitute the core functionalities of the `Atlas`
network. They collectively promote the security, stability, and development of a decentralized financial ecosystem in
the `Atlas` network.