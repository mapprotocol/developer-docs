# CLAUDE.md - Developer Docs

## Project Overview

This is the developer documentation repository for MAP Protocol, targeting **advanced developers** with detailed system design, protocol implementation principles, and underlying architecture.

## Target Audience

- Protocol developers
- Developers seeking deep understanding of MAP Protocol technical implementation
- Chain developers wanting to integrate with MAP Protocol
- Developers participating in protocol development or secondary development

## Documentation Structure

```
developer-docs/
├── overview/                    # Protocol Overview
│   ├── introduction.md          # MAP Protocol Introduction
│   ├── architecture.md          # Overall Architecture
│   └── v1-vs-v2.md              # v1 vs v2 Comparison and Evolution
│
├── fundamentals/                # Fundamentals
│   ├── accounts.md              # Accounts
│   ├── transactions.md          # Transactions
│   ├── blocks.md                # Blocks
│   ├── gas.md                   # Gas Fees
│   ├── mpt.md                   # MPT Tree
│   ├── rlp.md                   # RLP Encoding
│   ├── cross-chain-message.md   # Cross-chain Message
│   └── oracle.md                # Oracle
│
├── relay-chain/                 # MAP Relay Chain (Atlas)
│   ├── architecture.md          # Atlas Architecture Design
│   ├── consensus/               # Consensus Mechanism
│   ├── genesis-contracts/       # Genesis Contracts Design
│   └── precompile-contracts/    # Precompile Contracts
│
├── protocol-v1/                 # V1: Light Client Solution
│   ├── overview.md              # v1 Design Overview
│   ├── light-client/            # Light Client Design
│   ├── mos/                     # MOS Layer Design
│   └── chains-integration/      # Chain Integration Development
│
├── protocol-v2/                 # V2: TSS + Light Client Fusion
│   ├── overview.md              # v2 Design Overview
│   ├── architecture.md          # Overall Architecture
│   ├── roles/                   # System Roles (Validator, Maintainer, Relayer, LP)
│   ├── tss/                     # TSS Threshold Signature
│   ├── light-client-integration/# Light Client Integration
│   ├── vault/                   # Vault State Management
│   ├── contracts/               # Contract Design
│   ├── cross-chain/             # Cross-chain Flow
│   ├── gas.md                   # Gas Mechanism
│   ├── security.md              # Security Design
│   └── slashing.md              # Slashing Mechanism
│
└── appendix/                    # Appendix
    ├── glossary.md              # Glossary
    └── references.md            # References
```

## Version Description

### Protocol v1 (Light Client Solution)
- Cross-chain verification based on light clients
- Maintainer maintains light client state
- Messenger delivers cross-chain messages
- Components: Light Client, MOS, Compass

### Protocol v2 (TSS + Light Client Fusion)
- Based on TSS (Threshold Signature Scheme)
- Integrates light client for dual verification
- Maintainer participates in TSS signing
- Components: TSS, Vault, Gateway, Compass-TSS

## Content Scope

| Content Type | This Repo | docs Repo |
|-------------|-----------|-----------|
| Protocol Design Principles | ✅ Deep | Brief |
| Contract Design/ABI | ✅ | ❌ |
| Light Client Implementation | ✅ | ❌ |
| TSS Implementation Details | ✅ | ❌ |
| Chain Integration Development | ✅ | ❌ |
| Node Operation | ❌ | ✅ |
| API/SDK Usage | ❌ | ✅ |

## Writing Guidelines

1. **Language**: English
2. **Depth**: For advanced developers, include technical details, code examples, architecture diagrams
3. **Format**: Markdown with Mermaid diagrams support
4. **Code**: Provide complete code examples with explanations

## Related Repositories

- **docs**: User-facing documentation (node operation, API calls, etc.)
- **atlas**: MAP Relay Chain source code
- **compass**: Compass v1 source code
- **compass-tss**: Compass TSS source code
