# Web3 & Blockchain (Solana, Ethereum, DeFi) — Super Detailed Interview & Exam Notes

> 📌 **GitHub:** [nirajkr26](https://github.com/nirajkr26) &nbsp;|&nbsp; **LinkedIn:** [nirajkr26](https://www.linkedin.com/in/nirajkr26)

---

## 1. Introduction to Web3

**Web3** refers to a **decentralized web** where users control identity, assets, and data using **blockchains**, **smart contracts**, and **cryptographic wallets**.

**Core Components:**
- **Blockchain** — distributed ledger of transactions
- **Smart contracts** — on-chain programs
- **Tokens** — digital assets (fungible + NFTs)
- **Wallets** — key management + signing
- **dApps** — decentralized applications
- **Decentralized storage** — IPFS, Arweave

---

## 2. Blockchain Fundamentals

A blockchain is an **append-only, tamper-resistant** log of transactions replicated across a network.

**Block structure (simplified):**
```
Block = {
  header: { prev_hash, merkle_root, timestamp, nonce },
  transactions: [tx1, tx2, ...]
}
```

**Key properties:**
- **Immutability** via hashing + consensus
- **Transparency** and verifiability
- **Finality** (probabilistic or deterministic)

---

## 3. Cryptography Basics

### 3.1 Hash Functions
- One-way, deterministic, collision-resistant
- Used for block links, Merkle trees, PoW

### 3.2 Public/Private Keys
- Private key signs transactions
- Public key verifies signatures

### 3.3 Digital Signatures (ECDSA/Ed25519)
- Proves ownership without revealing private key

### 3.4 Merkle Trees
- Efficient proof of inclusion for transactions

---

## 4. Consensus Mechanisms

| Mechanism | Idea | Pros | Cons |
|-----------|------|------|------|
| Proof of Work (PoW) | Miners solve puzzles | High security | Energy-heavy, slower |
| Proof of Stake (PoS) | Validators stake tokens | Energy-efficient, fast | Rich-get-richer risk |
| Delegated PoS (DPoS) | Token holders elect validators | High throughput | More centralized |
| BFT (PBFT/Tower BFT) | Validators vote on blocks | Fast finality | Small validator sets |
| Proof of History (PoH) | Cryptographic clock (Solana) | High parallelism | Complex, new |

**Finality types:**
- **Probabilistic** — higher confirmation depth reduces reorg risk (Bitcoin)
- **Deterministic** — block finality guaranteed after consensus (Solana)

---

## 5. Data Models: UTXO vs Account

| Feature | UTXO (Bitcoin) | Account (Ethereum/Solana) |
|---------|----------------|---------------------------|
| State | Outputs are spent/unspent | Global account state |
| Parallelism | Easier to parallelize | More contention on hot accounts |
| Smart contracts | Limited | Native support |
| Example | Bitcoin | Ethereum, Solana |

---

## 6. Transactions & Fees

**Common fields:**
- `from`, `to`, `value`, `nonce`, `gas`, `signature`

**Ethereum fees (EIP-1559):**
- `baseFee` + `priorityFee` (tip)

**Solana fees:**
- Based on **compute units** and **priority fees**
- Transactions are **fast** but sensitive to network load

---

## 7. Smart Contracts

**Ethereum (EVM):**
- Smart contracts = bytecode running in a sandboxed VM
- Storage is persistent key-value

```solidity
contract Counter {
    uint public count = 0;
    function inc() public { count += 1; }
}
```

**Common patterns:**
- **Events** for logging
- **Access control** (`onlyOwner`)
- **Upgradeability** via proxies

---

## 8. Tokens & Standards

### 8.1 Ethereum
- **ERC-20** (fungible tokens)
- **ERC-721** (NFTs)
- **ERC-1155** (multi-token standard)

### 8.2 Solana
- **SPL Token** (fungible + NFT)
- Token metadata via Metaplex

---

## 9. DeFi Primitives

- **DEX/AMM**: Uniswap-style constant product `x*y=k`
- **Liquidity pools**: users provide assets, earn fees
- **Lending protocols**: collateralized loans
- **Stablecoins**: fiat-pegged (USDC/USDT) or algorithmic
- **Staking**: earn rewards for securing networks
- **Oracles**: bring off-chain data on-chain (Chainlink)

---

## 10. NFTs

NFTs represent unique digital ownership.
- **Metadata**: name, description, image URI
- **On-chain vs off-chain** metadata
- **Royalties**: enforced via marketplaces/standards

---

## 11. Scaling & Interoperability

**Layer-2 Solutions:**
- **Optimistic Rollups** (fraud proofs)
- **ZK Rollups** (validity proofs)

**Other techniques:**
- Sidechains
- State channels
- Sharding
- Modular blockchains (execution vs settlement layers)

---

## 12. Security & Risks

- **Private key security** (seed phrase = full control)
- **Reentrancy** and unsafe external calls
- **Integer overflow/underflow** (pre-Solidity 0.8)
- **MEV / front-running**
- **Bridge hacks** (largest DeFi losses)
- **Oracle manipulation**
- **Audits + formal verification** reduce risk

---

## 13. Solana Deep Dive

### 13.1 Architecture
- **Proof of History (PoH)** provides a verifiable time sequence
- **Tower BFT** consensus (PoS + PoH)
- **Sealevel** parallel smart contract runtime

### 13.2 Accounts Model
- All state is stored in **accounts**
- Programs are **stateless**; accounts hold data
- Each account has: owner program, lamports (SOL), data, rent epoch

### 13.3 Programs
- Smart contracts called **programs**
- Written in Rust/C and deployed as bytecode
- Interaction via **instruction data**

### 13.4 Transactions
- A transaction contains multiple **instructions**
- Parallel execution possible if accounts don’t conflict

### 13.5 Compute Units & Fees
- Each transaction has a **compute budget**
- Heavy programs must request more compute

### 13.6 Solana Tooling
- **solana-cli** for keys, airdrops, deployments
- **Anchor** framework for safer program development
- **solana/web3.js** SDK for client apps

---

## 14. Web3 Development Stack

- **Wallets:** MetaMask (EVM), Phantom (Solana)
- **RPC providers:** Infura, Alchemy, QuickNode
- **SDKs:** ethers.js, web3.js, solana/web3.js
- **Indexing:** The Graph, Helius, Covalent
- **Storage:** IPFS, Arweave

---

## 15. Governance & Tokenomics

- **Token supply**: fixed vs inflationary
- **Vesting schedules**: lockups to prevent dumps
- **On-chain governance**: proposals + voting
- **Staking rewards**: align incentives

---

## 16. Frequently Asked Questions (FAQs)

**Q1. What makes Web3 different from Web2?**
> Web3 is decentralized and ownership-based (wallets/tokens), while Web2 is centralized (platform-controlled identity/data).

**Q2. What is gas?**
> Gas measures computation; users pay fees to execute transactions and prevent spam.

**Q3. What is the difference between a wallet and an address?**
> A wallet manages keys; an address is a public identifier derived from the key.

**Q4. Why are smart contracts immutable?**
> Code deployed to a blockchain cannot be changed; upgrades require new contracts or proxy patterns.

**Q5. What is a dApp?**
> A decentralized app with on-chain logic + off-chain UI.

**Q6. What is MEV?**
> Maximum Extractable Value — profit from transaction ordering (front-running, sandwich attacks).

**Q7. Why are bridges risky?**
> They hold large pooled funds and rely on complex validation; they’re frequent attack targets.

**Q8. How is Solana different from Ethereum?**
> Solana uses PoH + parallel execution for high throughput; Ethereum prioritizes decentralization and security with EVM and rollups.

**Q9. What are oracles?**
> Services that provide off-chain data to smart contracts.

**Q10. What is an NFT?**
> A non-fungible token representing unique ownership of a digital asset.

---

## 17. Common Misconceptions

- ❌ *"Blockchain data is private"* → Public chains are transparent; privacy requires special protocols.
- ❌ *"Smart contracts are always secure"* → Bugs are permanent and exploits are common.
- ❌ *"All blockchains are the same"* → They differ in consensus, execution model, and performance.
- ❌ *"Web3 removes all trust"* → It shifts trust from institutions to code + cryptography.
- ❌ *"Solana is just faster Ethereum"* → Solana’s account model and runtime are fundamentally different.

---

## 18. Quick Revision Checklist

- [ ] Blockchain structure, hashing, Merkle trees
- [ ] Public/private keys, signatures, wallets
- [ ] Consensus: PoW vs PoS vs BFT vs PoH
- [ ] UTXO vs account model
- [ ] Gas/fees and transaction lifecycle
- [ ] Smart contracts, events, and upgrade patterns
- [ ] Token standards (ERC-20/721/1155, SPL)
- [ ] DeFi basics: AMM, lending, staking, oracles
- [ ] L2 scaling: optimistic vs ZK rollups
- [ ] Solana: PoH, Sealevel, accounts, programs
- [ ] Security risks: reentrancy, MEV, bridges

---

*Last updated: 2026 | Suitable for: university exams, software engineering interviews, Web3 & blockchain roles*
