# Web3, Blockchain, Ethereum, Solana & DeFi — Super Detailed Interview & Exam Notes

> 📌 **GitHub:** [nirajkr26](https://github.com/nirajkr26) &nbsp;|&nbsp; **LinkedIn:** [nirajkr26](https://www.linkedin.com/in/nirajkr26)

---

## 1. Web3 Overview

**Web3** is an internet paradigm where users own digital assets and identities through cryptographic keys and decentralized networks.

### 1.1 Web1 vs Web2 vs Web3
| Era | Main Characteristic | Ownership |
|-----|---------------------|-----------|
| Web1 | Read-only websites | Publishers |
| Web2 | Read/write + social platforms | Platforms |
| Web3 | Read/write/own with tokens + wallets | Users/communities |

### 1.2 Core Building Blocks
- Blockchains
- Smart contracts
- Wallets and signatures
- Tokens (fungible + non-fungible)
- dApps
- DAOs
- Decentralized storage and indexing

---

## 2. Blockchain Fundamentals

A blockchain is a **distributed, append-only ledger** maintained by independent nodes with a shared consensus protocol.

### 2.1 Block Structure
```
Block {
  header {
    parent_hash,
    state_root,
    tx_root,
    receipts_root,
    timestamp,
    block_number,
    proposer/miner,
    extra_data
  }
  transactions[]
}
```

### 2.2 Key Properties
- **Immutability:** changes are infeasible without redoing consensus work/stake majority.
- **Transparency:** state and transactions are auditable.
- **Censorship resistance:** no central gatekeeper.
- **Programmability:** contracts execute deterministic logic.

### 2.3 Nodes in a Network
- Full nodes (validate and store chain state)
- Light clients (verify with proofs)
- Archive nodes (historical state)
- Validators/miners (produce blocks)

---

## 3. Cryptography Essentials

### 3.1 Hash Functions
- Deterministic, one-way, collision-resistant.
- Used in block linking, Merkle trees, commitments.

### 3.2 Public-key Cryptography
- Private key: signs transaction.
- Public key/address: identity and verification target.

### 3.3 Digital Signatures
- Ethereum often uses secp256k1 + ECDSA.
- Solana primarily uses Ed25519.

### 3.4 Merkle Trees
- Leaf = transaction hash.
- Parent = hash(left + right).
- Efficient inclusion proofs (`O(log n)`).

---

## 4. Consensus Mechanisms

| Mechanism | How it works | Benefits | Trade-offs |
|-----------|--------------|----------|------------|
| PoW | Miners solve cryptographic puzzle | Battle-tested security | High energy, lower TPS |
| PoS | Validators stake tokens to propose/attest blocks | Energy efficient | Slashing/centralization concerns |
| DPoS | Delegated validators elected | Faster throughput | Lower decentralization |
| BFT variants | Validators vote for finality | Fast deterministic finality | Usually smaller validator sets |
| PoH + PoS (Solana) | Cryptographic clock + staking consensus | Very high throughput potential | Operational complexity |

### 4.1 Finality
- **Probabilistic finality:** confidence increases with confirmations (Bitcoin).
- **Deterministic finality:** block is final after quorum confirmation (many BFT systems).

### 4.2 Forks and Reorgs
- Temporary forks can occur due to network latency.
- Reorg depth matters for payment confirmation policies.

---

## 5. State Models: UTXO vs Account-Based

| Feature | UTXO | Account-based |
|---------|------|---------------|
| State representation | Unspent outputs | Address/account balances + storage |
| Parallelism | Naturally parallelizable | Depends on account contention |
| Contract expressiveness | Limited in pure UTXO chains | Rich contract model |
| Typical chains | Bitcoin | Ethereum, Solana |

---

## 6. Transactions, Mempool & Fees

### 6.1 Transaction Lifecycle
1. User signs transaction
2. Node validates signature/nonce/balance
3. Transaction enters mempool
4. Block producer includes tx
5. Block confirmed/finalized

### 6.2 Fee Models
- **Ethereum (EIP-1559):** base fee (burned) + priority fee (tip)
- **Solana:** base fee + priority fee + compute budget interactions

### 6.3 Nonce / Replay Protection
- Prevents duplicate/replay execution.
- Ensures ordered transaction semantics per account.

---

## 7. Ethereum Deep Dive

### 7.1 EVM Basics
- Stack-based virtual machine.
- Executes bytecode opcodes deterministically.
- Gas metering prevents infinite loops.

### 7.2 Ethereum Accounts
- **EOA** (Externally Owned Account): controlled by private key.
- **Contract Account:** controlled by bytecode logic.

### 7.3 Storage Model
- Storage slots (persistent, expensive)
- Memory (ephemeral during execution)
- Calldata (input bytes)
- Logs/events (indexed off-chain read path)

### 7.4 Solidity Example
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.24;

contract Vault {
    address public owner;
    mapping(address => uint256) public balance;

    constructor() {
        owner = msg.sender;
    }

    function deposit() external payable {
        balance[msg.sender] += msg.value;
    }

    function withdraw(uint256 amount) external {
        require(balance[msg.sender] >= amount, "insufficient");
        balance[msg.sender] -= amount;
        (bool ok, ) = msg.sender.call{value: amount}("");
        require(ok, "transfer failed");
    }
}
```

### 7.5 Common Solidity Patterns
- Checks-Effects-Interactions
- Access control (`Ownable`, `AccessControl`)
- Pausable emergency stops
- Pull over push payments
- Upgradeable proxies (UUPS/Transparent)

---

## 8. Solana Deep Dive

### 8.1 Solana Architecture
- **PoH (Proof of History):** cryptographic sequencing clock.
- **Tower BFT:** PoS-based voting over PoH timeline.
- **Sealevel runtime:** parallel execution on non-overlapping account sets.

### 8.2 Accounts Model
Every state is an account with:
- Owner program ID
- Lamports (SOL)
- Data bytes
- Executable flag

Programs are stateless executables; account data holds mutable state.

### 8.3 Program-Derived Addresses (PDAs)
- Deterministic addresses generated from seeds + program ID.
- No private key; program signs via runtime.
- Useful for authority/state ownership patterns.

### 8.4 Instruction + Transaction Model
- One transaction can include multiple instructions.
- Each instruction specifies required accounts (read/write/signer).
- Runtime enforces account locks for safety and parallelism.

### 8.5 Compute Budget
- Compute units cap program execution.
- Priority fees can improve inclusion during congestion.

### 8.6 Solana Tooling
- `solana-cli` for keypairs, cluster config, deploys
- `anchor` framework for Rust-based smart contracts
- `solana/web3.js` for client integrations

---

## 9. Smart Contract Security

### 9.1 Common Vulnerabilities (EVM)
- Reentrancy
- Access control bugs
- Unsafe `delegatecall`
- Price/oracle manipulation
- Signature replay/malleability issues
- Precision/rounding and decimal assumptions
- Upgrade proxy misconfiguration

### 9.2 Common Vulnerabilities (Solana)
- Account validation mistakes
- Missing signer checks
- PDA seed collisions or misuse
- CPI (cross-program invocation) trust assumptions
- Rent exemption/account ownership confusion

### 9.3 Defensive Practices
- Threat modeling per feature
- Unit + fuzz + invariant testing
- External audits
- Bug bounty programs
- On-chain monitoring and circuit breakers

---

## 10. Token Standards

### 10.1 Ethereum Standards
- **ERC-20:** fungible token balances/allowances
- **ERC-721:** unique NFTs
- **ERC-1155:** multi-token standard (batch efficient)
- **ERC-4626:** tokenized vaults

### 10.2 Solana Standards
- **SPL Token Program**
- Associated token accounts
- Metaplex metadata standard for NFTs/collections

### 10.3 Tokenomics Basics
- Total supply / circulating supply
- Emission schedule
- Vesting and cliffs
- Utility and governance rights

---

## 11. DeFi Core Primitives

### 11.1 AMMs and DEXs
- Constant product pools: `x * y = k`
- Concentrated liquidity variants (capital efficient)
- Slippage depends on pool depth and trade size

### 11.2 Lending Protocols
- Overcollateralized borrowing
- Health factor and liquidation thresholds
- Interest rate models (utilization-based)

### 11.3 Derivatives
- Perpetual swaps
- Options vaults
- Structured products

### 11.4 Stablecoins
- Fiat-backed
- Crypto-backed
- Algorithmic (higher depeg risk)

---

## 12. MEV (Max/ Miner/Validator Extractable Value)

MEV = value from controlling transaction ordering/inclusion.

### 12.1 Typical MEV Strategies
- Arbitrage
- Sandwich attacks
- Liquidation racing
- Backrunning

### 12.2 MEV Mitigation
- Private mempools / order flow auctions
- Frequent batch auctions
- Intent-based systems
- Slippage protection + anti-MEV UX patterns

---

## 13. NFTs and Digital Ownership

### 13.1 NFT Components
- Token ID
- Owner address
- Metadata URI
- Optional royalty metadata

### 13.2 Metadata Storage
- On-chain (expensive, durable)
- IPFS/Arweave (common)
- Centralized URLs (risk: mutability/unavailability)

### 13.3 NFT Use Cases
- Art and collectibles
- Tickets and membership passes
- Identity credentials
- Gaming assets

---

## 14. DAOs & Governance

### 14.1 Governance Flow
1. Proposal creation
2. Discussion period
3. Voting period
4. Timelock
5. Execution

### 14.2 Voting Models
- Token-weighted voting
- Quadratic voting
- Delegated voting

### 14.3 Governance Risks
- Voter apathy
- Whale dominance
- Governance attacks (flash-loan voting in weak systems)

---

## 15. Layer-2 & Scaling

### 15.1 Rollups
- **Optimistic rollups:** fraud proofs, challenge windows
- **ZK rollups:** validity proofs, faster finality assumptions

### 15.2 Other Scaling Paths
- Sidechains
- State channels
- App-specific chains
- Data availability layers + modular architecture

### 15.3 Scalability Trilemma
Trade-off among:
- Decentralization
- Security
- Scalability

---

## 16. Cross-chain Bridges & Interoperability

### 16.1 Bridge Types
- Lock/mint bridges
- Burn/mint bridges
- Liquidity network bridges

### 16.2 Bridge Security Challenges
- Multisig key compromise
- Validator collusion
- Smart contract bugs
- Message verification flaws

### 16.3 Safer Interop Design
- Minimize trust assumptions
- Multiple independent verifiers
- Rate limits and circuit breakers
- Continuous monitoring and pause controls

---

## 17. Wallets, Custody & Key Management

### 17.1 Wallet Types
- Hot wallets (software)
- Cold wallets (hardware/offline)
- Custodial wallets (provider-managed keys)
- Non-custodial wallets (user-managed keys)

### 17.2 Seed Phrase Best Practices
- Offline backup
- Never share seed/private key
- Multi-location secure storage

### 17.3 Advanced Controls
- Multisig wallets
- MPC custody
- Session keys and permissions

---

## 18. Web3 Application Architecture

### 18.1 dApp Layers
1. UI (React/Next.js/mobile)
2. Wallet adapter/signing layer
3. RPC + indexing layer
4. Smart contract/program layer
5. Analytics/monitoring layer

### 18.2 Data Sources
- Direct RPC reads
- Subgraphs/indexers (The Graph, Helius)
- Event processors
- Data warehouses for BI

### 18.3 Common dApp Performance Patterns
- Read caching
- Batch RPC calls
- Event-driven state sync
- Fallback RPC providers

---

## 19. Web3 Dev Toolchain

### 19.1 EVM Stack
- Solidity
- Hardhat / Foundry
- OpenZeppelin
- Ethers.js / Viem

### 19.2 Solana Stack
- Rust
- Anchor
- solana-cli
- solana/web3.js

### 19.3 Testing Types
- Unit tests
- Integration tests (localnet/testnet)
- Fork tests
- Fuzz/invariant/property tests

---

## 20. Compliance, Legal & Operational Considerations

- KYC/AML depending on jurisdiction and product class
- Securities law implications for token issuance
- Consumer protection and disclosures
- Tax reporting requirements
- Incident response and governance transparency

---

## 21. Production Risks & Incident Handling

### 21.1 Major Risk Categories
- Smart contract exploit
- Oracle failure
- Bridge compromise
- Key compromise
- Governance attack
- Liquidity crisis

### 21.2 Incident Response Checklist
- Pause vulnerable contracts (if available)
- Notify users transparently
- Coordinate with security/audit partners
- Snapshot impact and plan restitution
- Publish postmortem with corrective actions

---

## 22. Web3 Interview Questions (High-value)

1. Explain AMM pricing and impermanent loss.
2. Compare optimistic vs ZK rollups.
3. Describe a secure token launch design.
4. Explain reentrancy and mitigation.
5. Compare Ethereum account model with Solana accounts.
6. How would you secure a bridge?
7. How do you design a DAO governance process?
8. Explain MEV and user protections.

---

## 23. Frequently Asked Questions (FAQs)

**Q1. Is blockchain data private by default?**
> No. Public blockchains are transparent; privacy requires dedicated cryptographic systems and careful app design.

**Q2. What is gas?**
> Gas is the unit of computational work; users pay fees to execute operations and prevent spam.

**Q3. Why are smart contract bugs severe?**
> Contracts are hard to change once deployed, and exploits can be executed instantly and globally.

**Q4. What is finality?**
> The assurance that a confirmed transaction won’t be reversed.

**Q5. What is a DAO?**
> A community-governed organization coordinated by smart contracts and token-based voting.

**Q6. Why do people use Layer-2?**
> To reduce fees and improve throughput while inheriting base-layer security properties.

**Q7. What is impermanent loss?**
> Temporary value divergence LPs may face in AMMs versus simply holding assets.

**Q8. What makes Solana fast?**
> PoH sequencing + Sealevel parallel runtime + optimized network architecture.

**Q9. What are oracles in DeFi?**
> External data providers that feed prices/events to smart contracts.

**Q10. Are bridges safe?**
> They are critical but historically high-risk; security design and operational controls are essential.

---

## 24. Common Misconceptions

- ❌ *"Web3 removes trust entirely"* → It reduces some trust assumptions but introduces new ones (code, validators, bridges, oracles).
- ❌ *"All tokens are securities"* → Classification depends on jurisdiction, utility, and issuance model.
- ❌ *"Smart contracts are immutable so upgrades are impossible"* → Upgrade proxies and governance-controlled migration patterns exist.
- ❌ *"Solana and Ethereum are interchangeable"* → Their execution and state models differ significantly.
- ❌ *"Audited means hack-proof"* → Audits reduce risk; they do not guarantee safety.

---

## 25. Quick Revision Checklist

- [ ] Web1/Web2/Web3 differences and ownership model
- [ ] Block structure, hashing, Merkle proofs
- [ ] Consensus and finality types
- [ ] UTXO vs account-based chains
- [ ] EVM internals (gas, storage, calldata, logs)
- [ ] Solana architecture (PoH, Sealevel, accounts, PDAs)
- [ ] Token standards (ERC-20/721/1155, SPL)
- [ ] DeFi primitives (AMM, lending, stablecoins)
- [ ] MEV attack types and mitigation
- [ ] L2 rollups and scalability trilemma
- [ ] Bridge security assumptions
- [ ] Wallet custody and key management
- [ ] Smart contract security best practices

---

*Last updated: 2026 | Suitable for: university exams, software engineering interviews, Web3/blockchain/DeFi roles*
