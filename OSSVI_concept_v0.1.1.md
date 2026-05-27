# OSSVI — Open Source Value Infrastructure
### Concept Document v0.1.1 | Private Draft | May 27, 2026

> **Status:** Pre-development concept. This document exists to establish provenance and provide a planning foundation. Not for public release.
>
> **Changelog from v0.1:** Introduced User Hash ID primitive; refined Project Hash ID as the core asset unit (analogous to a token mint); added anti-bot / AI-ownership governance layer; restructured valuation model as emergent from on-chain registration rather than pre-scored signals; contributor model flagged as actively in redesign; two major design questions formally pinned for dedicated workshopping sessions.

---

## Overview

OSSVI (Open Source Value Infrastructure) is a proposed protocol-layer system that gives open source software projects a cryptographically unique, immutable identity — and couples that identity to a structured value and monetization framework. It is designed to finally close the gap between the enormous economic value that open source generates for the world and the near-zero value that flows back to the creators and maintainers of that work.

The core insight: an open source project's codebase is a real, utility-bearing asset. OSSVI treats it as such — not speculatively like an NFT, but with value derived from measurable, on-chain utility signals. The Project Hash ID *is* the asset. This is, fundamentally, what NFTs *should* have been: unique identity anchored to something real, owned by the people who built it.

---

## Problem Statement

Open source software powers an estimated **$8.8 trillion in economic value** annually yet the developers and maintainers behind it capture almost none of it. Existing monetization strategies — open core, SaaS wrappers, dual licensing, donations, sponsorships — all treat the project as a *service vehicle* rather than a *first-class asset*. None provide:

- A persistent, tamper-proof identity for a codebase
- A mechanism for the work itself (not a derivative service) to carry transferable economic value
- Automatic, transparent value distribution across verified contributors
- License compliance enforcement that scales across the full OSS dependency graph
- Protection against bot-mediated gaming of the system

The result is a structurally broken economy where trillion-dollar infrastructure is built on volunteer labor with no systemic value-return mechanism.

---

## The OSSVI Concept

OSSVI combines five interlocking primitives into one coherent system:

1. **User Hash ID** — Every verified human participant in the OSSVI ecosystem receives a unique, cryptographically derived identity anchor. This ID functions as their wallet, their reputation record, and their ownership claim across all projects they register. It is issued once, tied to verified platform accounts (GitHub, npm, etc.), and non-transferable.

2. **Project Hash ID** — Every registered project receives a unique hash derived from its codebase content, metadata, and owner identity. This hash is recorded immutably on-chain and becomes the project's permanent asset. The Project Hash ID *is* the asset — analogous to a token ID or NFT, but anchored to real utility rather than speculation. Its initial valuation is assigned at mint time and subsequently governed by on-chain dynamics. *(Initial valuation mechanism: see Pinned Question #2.)*

3. **Smart License Layer** — License terms (any existing SPDX license, or the novel OSSVI Genesis License) are encoded as smart contracts. Derivative works, forks, and commercial usages are detectable and governable by these contracts. The OSSVI license explicitly encodes: AI systems are not legal persons, cannot own assets, and cannot hold or accrue value in the protocol except as agents acting on behalf of a verified human User Hash ID.

4. **Utility-Weighted Valuation** — The on-chain value of a Project Hash ID reflects the project's real utility to the ecosystem — not speculation. High-influence, widely-depended-upon, ecosystem-critical projects are weighted more heavily. Trivial, redundant, or low-impact projects are weighted less, approaching zero. The weighting model is protocol-governed and updateable. *(Full signal design: see Pinned Question #2.)*

5. **Value Capture & Distribution** — Once an asset has on-chain value, that value can be staked, commercially licensed, or traded, with distributions routed to the project's registered contributors via their User Hash IDs according to contribution weighting. *(Payment mechanism: see Pinned Question #1. Contributor weighting model: see Open Design Question #3.)*

### The Self-Referential Bootstrap

The OSSVI protocol itself will be the **first registered asset in its own system** — a GitHub repository, licensed under the OSSVI Genesis License, and issued the first Project Hash ID at launch. This simultaneously serves as:

- The first proof of concept
- A live demonstration of the full identity stack
- The genesis asset establishing on-chain provenance for the entire protocol
- An immutable timestamp of origin that cannot be retroactively disputed

---

## System Architecture

### Layer Stack

| Layer | Function | Candidate Technologies |
|---|---|---|
| **Identity: User** | Verified human identity → User Hash ID (wallet + ownership registry) | BLAKE3 / SHA-256, platform OAuth verification (GitHub, npm), ZK-proof of humanity |
| **Identity: Project** | Codebase + metadata + owner → Project Hash ID (the asset) | BLAKE3, IPFS content addressing, Merkle trees |
| **Licensing** | Smart contracts encoding license terms, attribution, derivative rules, AI-ownership prohibition | Solidity (EVM) or Rust (Solana/Cosmos), SPDX registry integration |
| **Valuation** | On-chain scoring model assigning and updating project value | Oracle network, protocol-governed weighting; initial mint value TBD *(Pinned #2)* |
| **Monetization** | Asset staking, commercial licensing, trading with royalty splits | DeFi primitives, custom token standard (ERC-1155 or novel spec) |
| **Anti-Abuse** | Bot detection, botnet prevention, sybil resistance | Rate limiting, platform cross-verification, behavioral analysis |
| **Governance** | Protocol-level decision making for upgrades and disputes | Hybrid foundation + on-chain contributor voting |

### User Hash ID Scheme

The User Hash ID is a composite identity anchor constructed from:

```
USER_ID = Hash(
  platform_identity_set,        // verified GitHub + npm + other platform account IDs
  registration_timestamp,       // block timestamp at first registration
  registry_nonce,               // unique anti-collision nonce from protocol
  proof_of_humanity_signal      // ZK or oracle-attested human verification
)
```

Properties:
- One User Hash ID per human. Non-transferable.
- Acts as the user's protocol wallet — all owned Project Hash IDs are attached to it
- Ownership verified at registration by cross-referencing platform account ownership: a user may only register projects they demonstrably own or substantially control on GitHub, npm, or equivalent platforms
- Does not carry a monetary value itself — it is an identity and ownership container, not a tradeable asset
- Human-only: AI systems cannot hold a User Hash ID. Agentic coding tools operating on behalf of a human user act *through* that user's ID, not independently

### Project Hash ID Scheme

The Project Hash ID is a composite fingerprint constructed from:

```
PROJECT_ID = Hash(
  canonical_source_tree_hash,    // BLAKE3 of normalized codebase
  owner_user_hash_id,            // anchors the project to its human owner
  license_identifier,            // SPDX ID or OSSVI custom ID
  registration_timestamp,        // block timestamp at registration
  registry_nonce                 // unique anti-collision nonce from protocol
)
```

Properties:
- One Project Hash ID per project. The ID is the asset.
- Forks produce new Project Hash IDs with a lineage pointer to the parent. A fork does not inherit parent asset value but can accrue its own.
- The ID is reproducible and verifiable by any third party from source
- Contributor User Hash IDs are attached to the project record at registration, with contribution weighting stored on-chain. *(Weighting model: Open Design Question #3)*

### Valuation Model — Current Direction

The v0.1 draft proposed pre-defined utility signals (downloads, dependency depth, etc.) as the valuation input. This approach has known problems: signals are gameable, proxy-quality is uneven, and the mechanism for converting scores into actual value flow was unspecified.

**Updated direction:** The Project Hash ID functions analogously to a token or NFT mint. Initial value is assigned at registration time by the protocol itself — not by external signals alone. The on-chain dynamics of the protocol (staking, licensing, trading, adoption) then govern value evolution post-mint, similar to how a token's value evolves after its initial issuance.

The key design requirement: high-influence / ecosystem-critical projects should receive meaningfully higher initial valuations than trivial or redundant projects. The gradient should run from "core Linux kernel dependencies" → "popular utility libraries" → "niche tools" → "one-off vibecoded experiments" → effectively zero.

The mechanism for computing this initial valuation, and for continuously updating it, is the central open design problem. It must be:
- **Resistant to gaming** — signals cannot be trivially inflated
- **Meaningful** — the gradient must be steep enough to differentiate real projects from noise
- **Transparent** — verifiable by any participant
- **Updateable** — governance can adjust weights as the ecosystem evolves

*(Full design workshopping: **Pinned Question #2**)*

---

## Anti-Bot & AI-Ownership Governance

This is a first-class design requirement, not an afterthought.

### AI Ownership Prohibition

The OSSVI Genesis License and protocol rules explicitly establish:

- AI systems, agents, and bots are not legal persons and cannot own, register, or accrue value from OSSVI assets
- A Project Hash ID must be owned by a verified human User Hash ID
- Agentic coding tools (Cursor, Claude Code, Copilot, etc.) may be used in project development without restriction — using AI to write code does not affect a project's registrability
- The prohibition is on *autonomous AI ownership and value accrual*, not on *AI-assisted human development*

This is encoded at the license layer, not just as a policy statement.

### Sybil & Botnet Resistance

Known attack vectors and mitigations:

| Attack | Mitigation |
|---|---|
| Bot farm creating mass User Hash IDs | Platform OAuth cross-verification (real GitHub/npm accounts required); proof-of-humanity layer (ZK attestation or equivalent); registration rate limiting |
| Coordinated fake download/dependency inflation to boost valuation | Signal design must weight signals that are hard to fake at scale; on-chain anomaly detection; stake-weighted reputation |
| Trivial project mass-registration to flood the registry | Minimum registration requirements (project age, activity floor, platform history); near-zero initial valuation for projects that don't meet thresholds |
| Agentic bots acting on behalf of a human to game contribution weighting | All agentic actions attributed to the sponsoring human User Hash ID; rate limiting on contribution registrations per ID per time window |

The anti-abuse layer will require ongoing governance attention — this is not a solved problem at launch, but the architecture must accommodate it from day one.

---

## Design Principles

### Non-Negotiable

- **Opt-in only** — Existing MIT/Apache/GPL projects are not affected. Registration is voluntary. The open source grant of any existing license is never altered by OSSVI registration.
- **Human ownership only** — User Hash IDs and Project Hash IDs are owned by verified humans. AI systems cannot hold protocol assets independently.
- **Retroactive provenance** — Any project can register, including existing mature projects. Contributor weighting is computed from available git history at registration time.
- **Forkability preserved** — Forks produce new Project Hash IDs with lineage links to the parent. Forking remains a first-class right.

### Strongly Preferred

- **Layer-2 or app-specific chain** — Ethereum mainnet gas costs are a known barrier to OSS-scale adoption. A purpose-built OP Stack rollup, Cosmos app-chain, or equivalent L2 is the preferred execution environment.
- **Identity-first rollout** — The User Hash ID and Project Hash ID layers provide standalone utility (namespace integrity, provenance timestamps, fork lineage) independent of the value layer. Lead with identity; let monetization follow adoption.
- **License compatibility matrix** — The smart contract layer must encode SPDX license compatibility rules to govern derivative work licensing automatically.
- **Dual-mode design** — OSSVI wraps *any* existing license without replacing it. The value layer is additive, not substitutive.

---

## Known Challenges

### ⚠️ Pinned Question #1 — Value Capture / Payment Mechanism

*How does value actually enter the system from commercial users?*

Smart contracts can govern on-chain behavior between willing participants. They cannot compel a corporation in Germany to pay royalties for software it uses under an MIT license. The gap between "OSSVI assigns a value to a project" and "that value flows to maintainers" is the central unsolved design problem.

This question is deferred for dedicated workshopping. Options to explore include: voluntary commercial licensing tiers, protocol-enforced licensing for new OSSVI-licensed projects, ecosystem fund models, and others. No architecture decisions dependent on this mechanism should be finalized until it is resolved.

### ⚠️ Pinned Question #2 — Initial Valuation & Signal Design

*What determines a Project Hash ID's initial on-chain value at mint, and how does it evolve?*

The updated model treats the Project Hash ID as a token-like mint event. The initial value must reflect real ecosystem influence — core/critical projects high, noise projects near zero — but the mechanism for computing this is unresolved. Signal design, oracle architecture, and gaming resistance are all open. This is the second critical workshopping item.

### Open Design Question #3 — Contributor Model

The v0.1 commit-weight model (lines of code, commit count) is acknowledged as a poor proxy for actual contribution value and is not carried forward as the design direction. The contributor model is under active reconsideration. Leading candidate directions:

- **Flat owner-weighted** — Project Hash ID value accrues entirely to the registered owner's User Hash ID; contributors are handled off-chain or via separate voluntary agreements
- **Role-tiered** — Defined roles (core maintainer, significant contributor, occasional contributor) with fixed protocol-governed percentage splits per role
- **Governance-declared** — Project owner declares contributor splits at registration; protocol enforces the declared distribution but doesn't compute it

The right model must be gaming-resistant, simple enough to implement in v1, and not create perverse incentives for contribution inflation. Decision deferred pending Payment Mechanism resolution *(Pinned #1)*, as the contributor model is downstream of knowing what is actually being distributed.

### Retroactivity & Ownership Ambiguity

Millions of existing OSS projects have no single clear owner and complex multi-decade contributor histories. An **OSSVI Contributor Agreement (OCA)** — analogous to a CLA but granting value participation rights — will likely be required for projects seeking full monetization features. Projects with ambiguous ownership can register for identity/provenance features without activating the value layer.

### Community Trust

The OSS community is culturally allergic to financialization — and this objection is principled, not merely aesthetic. The strongest form of the objection: *"This is enclosure of the commons. The first person to register a project gets to own it."*

OSSVI's answer must be architectural, not just rhetorical:
- Opt-in removes the enclosure objection (no project is registered without the owner's action)
- Identity-first rollout demonstrates utility before monetization is introduced
- Human-only ownership and contributor-weighted distribution frames this as *creator value capture*, not *asset speculation*
- Governance non-transferability (contribution rights cannot be purchased) blocks plutocratic capture

### Gas / Transaction Cost Scaling

On-chain license compliance at the scale of the full OSS ecosystem faces real gas cost barriers on existing L1s. An app-specific chain or L2 with near-zero transaction costs is a prerequisite, not a nice-to-have.

---

## Differentiation from Prior Art

| System | What It Does | What It Lacks |
|---|---|---|
| **FOSS-chain** (2025) | Blockchain + smart contracts for OSS license compliance, function-level SHA-256 hashing | No economic value layer; no asset valuation |
| **Tokenized OSS Licenses** (concept) | Licensing rights as blockchain tokens for traceability | No utility-anchored valuation; adoption challenges |
| **Gitcoin** | Crypto-based OSS funding via grants | Donation-based, not asset-based; no project identity layer |
| **NFTs** | Unique on-chain digital asset ownership | Purely speculative value; no utility anchor; no licensing layer |
| **Open Core / SaaS** | Monetization via derivative commercial products | Treats project as service vehicle, not first-class asset |
| **ORCID / DOI** | Persistent identity for academic works and authors | Identity only; no value layer; not designed for code |
| **OSSVI** | Identity (user + project) + licensing + utility valuation + value distribution + bot governance | *The full stack* |

---

## Governance

The governance model has significant downstream implications and is an active design question. Three candidate models remain on the table:

**Protocol Foundation** (e.g., Apache/Linux Foundation model) — Neutral, established credibility, slower-moving, at risk of capture by large corporate sponsors.

**DAO with On-Chain Governance** — Contributor-voted, maximally decentralized, but susceptible to low participation and plutocratic token concentration.

**Open Standards Body** (e.g., SPDX/OSI model) — Maximum community trust, but slow to evolve and lacks execution capability.

**Recommended v1 approach:** A lightweight founding foundation operating the protocol and registry infrastructure, with on-chain contributor voting for protocol parameter changes (signal weights, fee schedules, license registry updates). Governance rights are **non-transferable** and tied to verified contribution records — not purchasable. This combination targets community trust (transparent governance, no corporate capture) while maintaining execution capability.

---

## Roadmap (Rough)

### Phase 0 — Concept & Provenance *(Active — Now → Q3 2026)*
- [x] Initial concept document (v0.1) — May 27, 2026
- [x] Private GitHub repository established — OSSVI repo, May 27, 2026
- [ ] Concept document v0.1.1 — refines architecture, adds User Hash ID, bot governance
- [ ] Workshopping sessions: Pinned Question #1 (Payment Mechanism), Pinned Question #2 (Valuation/Signals)
- [ ] Contributor model decision (Open Design Question #3)
- [ ] OSSVI Genesis License — draft and review
- [ ] Chain/L2 evaluation — preliminary shortlist

### Phase 1 — Protocol Design *(Q3 → Q4 2026)*
- [ ] Formal specification of the five-layer architecture
- [ ] Chain selection finalized
- [ ] User Hash ID specification — platform verification flow, ZK/humanity proof design
- [ ] Project Hash ID specification — normalization rules, canonical hash construction
- [ ] Valuation oracle design and initial mint mechanism
- [ ] Smart contract architecture draft
- [ ] Anti-abuse framework specification
- [ ] SPDX license compatibility matrix encoding plan

### Phase 2 — Reference Implementation *(Q4 2026 → Q1 2027)*
- [ ] OSSVI registry smart contracts (testnet)
- [ ] CLI tool for project registration and ID generation
- [ ] Self-registration: OSSVI repo issues its own first Project Hash ID
- [ ] User Hash ID verification flow (GitHub/npm OAuth integration)
- [ ] Valuation oracle prototype
- [ ] Contributor graph tooling (under revised model)

### Phase 3 — Private Beta & Hardening *(Q1 → Q2 2027)*
- [ ] Invite cohort of OSS maintainers for private beta
- [ ] Security audits of smart contracts
- [ ] Anti-abuse stress testing
- [ ] Gas optimization on chosen L2
- [ ] Governance framework finalization
- [ ] Legal review of Contributor Agreement (OCA)

### Phase 4 — Public Launch *(Q2 2027+)*
- [ ] Public registry launch
- [ ] OSSVI Genesis Asset goes live as Asset #1
- [ ] OSI/SPDX outreach and standards coordination
- [ ] Developer documentation and SDK release

---

## Open Questions

### Pinned (Dedicated Workshopping Required)
- **[PIN-1]** What is the actual value capture / payment mechanism? Who pays, when, triggered by what, enforced how?
- **[PIN-2]** What determines a Project Hash ID's initial on-chain valuation at mint? What signals, weights, and oracle architecture makes this gaming-resistant and meaningful?

### Active Design Questions
- **[ADQ-3]** Contributor model: flat owner-weighted, role-tiered, or governance-declared? What is "contribution" for non-code work?
- **[ADQ-4]** Chain/L2 selection: Solana, OP Stack rollup, Cosmos app-chain? Evaluation criteria: cost, speed, community alignment, smart contract maturity.
- **[ADQ-5]** Should forks inherit any fraction of parent asset value at fork time? If yes, what fraction and under what conditions?
- **[ADQ-6]** How are projects with corporate CLAs handled (Google, Red Hat, etc.) where contributors may have signed away IP rights?
- **[ADQ-7]** Governance token design: purely governance-scoped, unit of account, or both? This decision gates the full economic architecture.
- **[ADQ-8]** Minimum registration requirements: what floor prevents trivial project flooding without raising the bar high enough to exclude legitimate small projects?

---

## Provenance Declaration

This document was created on **May 27, 2026** by Mikeal Clark as the evolving concept record for OSSVI. Version 0.1 was the initial concept. Version 0.1.1 refines the primitive architecture (User Hash IDs, Project Hash ID as token-equivalent asset, AI-ownership governance), pins the two major unresolved design questions, and restructures the valuation model direction. The OSSVI project itself will, upon launch, be registered as Asset #1 in its own registry — a self-referential, self-validating proof of origin.

---

*OSSVI Concept Document v0.1.1 — Private / Pre-Release — All rights reserved pending licensing decision*
