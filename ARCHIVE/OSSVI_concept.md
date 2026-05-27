# OSSVI — Open Source Value Infrastructure
### Concept Document v0.1 | Private Draft | May 27, 2026

> **Status:** Pre-development concept. This document exists to establish provenance and provide a planning foundation. Not for public release.

---

## Overview

OSSVI (Open Source Value Infrastructure) is a proposed protocol-layer system that gives open source software projects a cryptographically unique, immutable identity — and couples that identity to a structured value and monetization framework. It is designed to finally close the gap between the enormous economic value that open source generates for the world and the near-zero value that flows back to the creators and maintainers of that work.

The core insight: an open source project's codebase is a real, utility-bearing asset. OSSVI treats it as such — not speculatively like an NFT, but with value derived from measurable, on-chain utility signals. This is, fundamentally, what NFTs *should* have been.

This document outlines the concept, architecture, key design principles, known challenges, and a development roadmap.

---

## Problem Statement

Open source software powers an estimated **$8.8 trillion in economic value** annually yet the developers and maintainers behind it capture almost none of it.[cite:9] Existing monetization strategies — open core, SaaS wrappers, dual licensing, donations, sponsorships — all treat the project as a *service vehicle* rather than a *first-class asset*.[cite:5][cite:9] None provide:

- A persistent, tamper-proof identity for a codebase
- A mechanism for the work itself (not a derivative service) to carry transferable economic value
- Automatic, transparent value distribution across all contributors proportional to their contribution
- License compliance enforcement that scales across the full OSS dependency graph

The result is a structurally broken economy where trillion-dollar infrastructure is built on volunteer labor with no systemic value-return mechanism.

---

## The OSSVI Concept

OSSVI combines four interlocking primitives into one coherent system:

1. **Cryptographic Identity** — Every registered project receives a unique hash derived from its codebase content, metadata, and contributor graph. This hash is recorded immutably on-chain and becomes the project's permanent, forkable identity anchor.

2. **Smart License Layer** — License terms (any existing SPDX license, or a new custom OSSVI license) are encoded as smart contracts. Derivative works, forks, and commercial usages are automatically detected and governed by these contracts, enabling on-chain license compliance without manual enforcement.[cite:10]

3. **Utility-Anchored Valuation** — Unlike NFTs whose value is purely speculative, an OSSVI asset's value is derived from quantifiable utility signals: download counts, dependency graph depth, CVE criticality, enterprise adoption, contributor activity. These signals are fed on-chain via oracles and continuously update the asset's value score.

4. **Value Capture & Distribution** — Once an asset has an on-chain value, that value can be staked, commercially licensed, or traded — with royalty splits automatically routed back through the contributor graph based on commit weight analysis.

### The Self-Referential Bootstrap

The OSSVI protocol itself will be the **first registered asset in its own system** — a GitHub repository, openly licensed (either under an existing license or a novel OSSVI Genesis License), and issued its own Value Hash ID at launch. This simultaneously serves as:

- The first proof of concept
- A live demonstration of the full stack
- The genesis asset establishing on-chain provenance for the entire protocol
- A timestamp of origin that cannot be retroactively disputed

---

## System Architecture

### Layer Stack

| Layer | Function | Candidate Technologies |
|---|---|---|
| **Identity** | Hash fingerprint of codebase + metadata = unique on-chain anchor | BLAKE3 / SHA-256, IPFS content addressing, Merkle trees |
| **Licensing** | Smart contracts encoding license terms, attribution, derivative rules | Solidity (EVM) or Rust (Solana/Cosmos), SPDX registry integration |
| **Valuation Oracle** | On-chain scoring model ingesting utility metrics | Chainlink-style oracle network, custom scoring algorithm |
| **Monetization** | Asset staking, commercial licensing, trading with royalty splits | DeFi primitives, custom token standard (ERC-1155 or novel spec) |
| **Governance** | Protocol-level decision making for upgrades and disputes | To be determined — see Governance section |

### Identity Hashing Scheme

The OSSVI Value Hash ID is a composite fingerprint constructed from:

```
OSSVI_ID = Hash(
  canonical_source_tree_hash,    // BLAKE3 of normalized codebase
  contributor_graph_fingerprint, // weighted Merkle of commit history
  license_identifier,            // SPDX ID or OSSVI custom ID
  registration_timestamp,        // block timestamp at registration
  registry_nonce                 // unique anti-collision nonce from protocol
)
```

This structure ensures:
- Two identical codebases with different contributor histories produce different IDs
- The ID is reproducible and verifiable by any third party from source
- Forks produce related but distinct IDs, preserving lineage traceability

### Valuation Model

Asset value \(V\) is computed as a continuously updated weighted score:

\[V = \sum_{i} w_i \cdot s_i\]

Where \(s_i\) are normalized utility signals (downloads, dependents, adoption breadth, contributor health) and \(w_i\) are protocol-governed signal weights, updateable via governance. The key property: \(V\) has a **real utility floor** — a project with millions of downstream dependents cannot rationally be worth zero regardless of market sentiment.

---

## Design Principles

### Non-Negotiable

- **Opt-in only** — Existing MIT/Apache/GPL projects are not affected. Registration is voluntary. The open source grant of any existing license is never altered by OSSVI registration.
- **Contributor-weighted ownership** — Value accrues to the contributor graph, not just the repository owner. Commit-weight analysis (lines contributed, issue resolution, review history) determines proportional stakes.
- **Retroactive provenance** — Any project can register, including existing mature projects, but contributor weighting is computed from available git history at registration time.
- **Forkability preserved** — Forks produce new OSSVI IDs with lineage links to the parent. A fork does not inherit the parent's asset value but can accrue its own. Forking remains a first-class right.

### Strongly Preferred

- **Layer-2 or app-specific chain** — Ethereum mainnet gas costs are a known barrier to OSS-scale adoption.[cite:10] A purpose-built OP Stack rollup, Cosmos app-chain, or equivalent L2 is the preferred execution environment.
- **License compatibility matrix** — The smart contract layer must encode SPDX license compatibility rules to govern derivative work licensing automatically.[cite:10]
- **Dual-mode design** — OSSVI wraps *any* existing license without replacing it. The value layer is additive, not substitutive.

---

## Known Challenges

### Retroactivity & Ownership Ambiguity

Millions of existing OSS projects have no single clear owner and complex multi-decade contributor histories. Assigning value proportionally at registration time requires both technical (git blame at scale) and legal (contributor agreement) solutions. A **OSSVI Contributor Agreement (OCA)** — analogous to a CLA but granting value participation rights — will likely be required for projects seeking full monetization features.

### The Forking Problem

OSS culture is built on the right to fork. Any system that makes asset value contingent on maintaining a canonical version creates perverse incentives for maintainers to suppress competition. OSSVI must be architected so that the protocol *benefits from* forks (each fork is a new registered asset, generating protocol fees) rather than being threatened by them.

### Gas / Transaction Cost Scaling

FOSS-chain research (2025) demonstrated that on-chain license compliance at the scale of the full OSS ecosystem (millions of packages, billions of dependency relationships) faces real gas cost barriers on existing L1s.[cite:10] An app-specific chain or L2 with near-zero transaction costs is a prerequisite, not a nice-to-have.

### Community Trust

The OSS community is culturally allergic to financialization. Any system perceived as "Wall Street meets GitHub" will face rejection before technical merits are evaluated.[cite:1] OSSVI must lead with governance transparency, contributor benefit, and the explicit framing that this is about *creators capturing value they already generate* — not speculation.

### License Complexity

There are 550+ recognized OSS licenses in the SPDX registry.[cite:10] Encoding full compatibility rules into smart contracts at scale is a significant engineering challenge requiring close coordination with the OSI and SPDX communities.

---

## Differentiation from Prior Art

| System | What It Does | What It Lacks |
|---|---|---|
| **FOSS-chain** (2025) | Blockchain + smart contracts for OSS license compliance, function-level SHA-256 hashing[cite:10] | No economic value layer; no asset valuation |
| **Tokenized OSS Licenses** (concept) | Licensing rights as blockchain tokens for traceability[cite:8] | No utility-anchored valuation; adoption challenges |
| **Gitcoin** | Crypto-based OSS funding via grants | Donation-based, not asset-based; no project identity layer |
| **NFTs** | Unique on-chain digital asset ownership | Value is purely speculative; no utility anchor; no licensing layer |
| **Open Core / SaaS** | Monetization via derivative commercial products[cite:9][cite:13] | Treats project as service vehicle, not first-class asset |
| **OSSVI** | Identity + licensing + utility valuation + value distribution | *The full stack* — this is what is being built |

---

## Governance

The governance model is an open design question with significant implications. Three candidate models:

**Protocol Foundation** (e.g., Apache/Linux Foundation model) — Neutral, established credibility, slower-moving, at risk of capture by large corporate sponsors.

**DAO with On-Chain Governance** — Contributor-voted, maximally decentralized, but susceptible to low participation and plutocratic token concentration.

**Open Standards Body** (e.g., SPDX/OSI model) — Maximum community trust, but slow to evolve and lacks execution capability.

The recommended approach for v1 is a **hybrid**: a small, lightweight founding foundation that operates the protocol and registry infrastructure, with on-chain contributor voting for protocol parameter changes (signal weights, fee schedules, license registry updates). Governance rights are non-transferable and tied to verified contribution records — not purchasable.

---

## Roadmap (Rough)

### Phase 0 — Concept & Provenance (Now → Q3 2026)
- [ ] Finalize concept document (this file)
- [ ] Establish private GitHub repository for provenance timestamp
- [ ] Draft OSSVI Genesis License (or select existing license)
- [ ] Design OSSVI_ID hashing specification in detail
- [ ] Research contributor agreement legal requirements

### Phase 1 — Protocol Design (Q3 → Q4 2026)
- [ ] Formal specification of the four-layer architecture
- [ ] Chain selection and L2/app-chain evaluation
- [ ] Valuation oracle design and signal weighting model
- [ ] Smart contract architecture draft
- [ ] SPDX license compatibility matrix encoding plan

### Phase 2 — Reference Implementation (Q4 2026 → Q1 2027)
- [ ] OSSVI registry smart contracts (testnet)
- [ ] CLI tool for project registration and ID generation
- [ ] Self-registration: OSSVI repo issues its own first OSSVI_ID
- [ ] Contributor graph analysis tooling
- [ ] Valuation oracle prototype

### Phase 3 — Private Beta & Hardening (Q1 → Q2 2027)
- [ ] Invite cohort of OSS maintainers for private beta
- [ ] Security audits of smart contracts
- [ ] Gas optimization on chosen L2
- [ ] Governance framework finalization
- [ ] Legal review of Contributor Agreement

### Phase 4 — Public Launch (Q2 2027+)
- [ ] Public registry launch
- [ ] OSSVI Genesis Asset goes live as first registered project
- [ ] OSI/SPDX outreach and standards coordination
- [ ] Developer documentation and SDK release

---

## Open Questions

- What chain/L2 provides the best cost, speed, and community alignment? (Solana, OP Stack, Cosmos?)
- Should the OSSVI token (if any) be a governance token, a value-of-account unit, or both?
- How is "contribution" defined for non-code work — documentation, issue triage, design, community management?
- What is the minimum viable signal set for v1 valuation scoring?
- Should forks inherit *any* fraction of parent asset value (e.g., 10% at fork time, diminishing)?
- How does the system handle projects with corporate CLAs (e.g., Google, Red Hat contributors)?

---

## Provenance Declaration

This document was created on **May 27, 2026** by Mikeal Clark as the initial concept record for OSSVI. Its existence in the private GitHub repository establishes the earliest known timestamp for this concept. The OSSVI project itself will, upon launch, be registered as Asset #1 in its own registry — creating a self-referential, self-validating proof of origin.

---

*OSSVI Concept Document v0.1 — Private / Pre-Release — All rights reserved pending licensing decision*
