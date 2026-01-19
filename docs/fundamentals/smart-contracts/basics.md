---
title: Smart Contract Basics
description: Solidity language, contract anatomy, libraries, and composability.
lang: en
---

This guide covers the fundamentals of smart contracts on MAPO-Relay-Chain, including the Solidity language, contract structure, libraries, and composability.

## Solidity Language

MAPO smart contracts primarily use Solidity as the programming language.

- Object-oriented, high-level language for implementing smart contracts
- Curly-bracket syntax influenced by C++
- Statically typed (variable types known at compile time)
- Supports inheritance, libraries, and complex user-defined types

### Example Contract

```solidity
// SPDX-License-Identifier: GPL-3.0
pragma solidity >= 0.7.0;

contract Coin {
    address public minter;
    mapping (address => uint) public balances;

    event Sent(address from, address to, uint amount);

    constructor() {
        minter = msg.sender;
    }

    function mint(address receiver, uint amount) public {
        require(msg.sender == minter);
        balances[receiver] += amount;
    }

    function send(address receiver, uint amount) public {
        require(amount <= balances[msg.sender], "Insufficient balance.");
        balances[msg.sender] -= amount;
        balances[receiver] += amount;
        emit Sent(msg.sender, receiver, amount);
    }
}
```

### Solidity Resources

- [Documentation](https://docs.soliditylang.org/en/latest/)
- [Solidity by Example](https://docs.soliditylang.org/en/latest/solidity-by-example.html)
- [OpenZeppelin Contracts](https://docs.openzeppelin.com/contracts)

## Contract Anatomy

A smart contract is a program that runs at an address on MAPO. It's made up of data and functions that execute upon receiving a transaction.

### Data Storage

**Storage** - Persistent data stored permanently on the blockchain:
```solidity
contract SimpleStorage {
    uint storedData; // State variable
}
```

**Memory** - Temporary data for function execution lifetime, much cheaper to use.

**Environment Variables** - Special global variables:
| Variable | Type | Description |
|----------|------|-------------|
| `block.timestamp` | uint256 | Current block epoch timestamp |
| `msg.sender` | address | Sender of the message (current call) |

### Functions

Functions can get or set information in response to transactions:

- **internal** - Don't create an EVM call, only accessible within contract
- **external** - Create an EVM call, part of contract interface
- **public** - Can be called internally or externally
- **private** - Only visible within defining contract

```solidity
// View function - doesn't modify state
function balanceOf(address _owner) public view returns (uint256) {
    return ownerBalance[_owner];
}

// State-changing function
function update_name(string value) public {
    dapp_name = value;
}
```

### Constructor

Executed once when the contract is first deployed:
```solidity
constructor() public {
    owner = msg.sender;
}
```

### Events and Logs

Events communicate with frontend applications:
```solidity
event Transfer(address from, address to, uint amount);
emit Transfer(msg.sender, receiver, amount);
```

## Libraries

Smart contract libraries provide reusable building blocks so you don't have to reinvent the wheel.

### Common Patterns

**Ownable** - Restricts access to contract owner:
```solidity
contract Ownable {
    address public owner;
    
    constructor() internal {
        owner = msg.sender;
    }
    
    modifier onlyOwner() {
        require(owner == msg.sender, "Ownable: caller is not the owner");
        _;
    }
}
```

**SafeMath** - Arithmetic with overflow checks (built into Solidity 0.8+)

### Using Libraries

```solidity
import "@openzeppelin/contracts/token/ERC721/ERC721.sol";

contract MyNFT is ERC721 {
    constructor() ERC721("MyNFT", "MNFT") public { }
}
```

### Popular Libraries

| Library | Description |
|---------|-------------|
| [OpenZeppelin](https://docs.openzeppelin.com/contracts/) | Most popular library for secure smart contract development |
| [DappSys](https://dappsys.readthedocs.io/) | Safe, simple, flexible building-blocks |

## Composability

Smart contracts are public and can be thought of as open APIs. You can use existing contracts as building blocks for your project.

### Principles

1. **Modularity** - Each contract performs a specific task
2. **Autonomy** - Contracts operate independently and are self-executing
3. **Discoverability** - Contracts are open-source and publicly available

### Benefits

- **Shorter development cycle** - Reuse existing solutions
- **Greater innovation** - Focus on new features instead of basic functionality
- **Better user experience** - Interoperability between dapps

### Example: Flash Loans

Flash loans demonstrate composability - borrowing assets without collateral, using them across multiple protocols, and repaying within one transaction. This requires combining calls to multiple contracts.

## Further Reading

- [Solidity Documentation](https://solidity.readthedocs.io/)
- [OpenZeppelin Contracts](https://docs.openzeppelin.com/contracts)
- [Composability is Innovation](https://future.a16z.com/how-composability-unlocks-crypto-and-everything-else/)
