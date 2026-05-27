# OSSVI — Open Source Value Infrastructure
### Concept Document v0.1.2 | Private Draft | May 27, 2026

> **Status:** Pre-development concept. This document exists to establish provenance and provide a planning foundation. Not for public release.
>
> **Changelog from v0.1.1:** Resolved ADQ-4 (chain selection) — external L1/L2 replaced with OSSVI-native PQC Registry Network. Introduced `did:ossvi` as OSSVI's own DID method. Defined the OSSVI Primitive Layer — a minimal post-quantum cryptographic substrate owned by the protocol itself. Inverted the relationship with Project Aegis: OSSVI defines the primitives; a broader PQC ecosystem may grow organically around them rather than OSSVI depending on an external infrastructure stack to ship first. Updated Layer Stack, differentiation table, and roadmap accordingly. PIN-1 and PIN-2 remain pinned.

---

## Overview

OSSVI (Open Source Value Infrastructure) is a proposed protocol-layer system that gives open source software projects a cryptographically unique, immutable identity — and couples that identity to a structured value and monetization framework. It is designed to finally close the gap between the enormous economic value that open source generates for the world and the near-zero value that flows back to the creators and maintainers of that work.

The core insight: an open source project's codebase is a real, utility-bearing asset. OSSVI treats it as such — not speculatively like an NFT, but with value derived from measurable, on-chain utility signals. The Project Hash ID *is* the asset.

**What NFTs should have been.** Unique identity anchored to something real, owned by the people who built it, quantum-resistant from day one, no gas fees, no speculative market required to give it meaning.

OSSVI is also, incidentally, the foundation of a post-quantum cryptographic identity and registry infrastructure. That is not the pitch — it is an emergent consequence of doing the protocol correctly.

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

## The OSSVI Primitive Layer

Before describing the protocol, it is necessary to describe what OSSVI is *built on* — because OSSVI defines this substrate itself rather than inheriting it from an external stack.

The OSSVI Primitive Layer is a minimal set of post-quantum cryptographic capabilities that the protocol owns and specifies. It is not a dependency on any existing project. It is the lowest layer of OSSVI.

### Cryptographic Primitives

| Primitive | Algorithm | Purpose |
|---|---|---|
| **Digital Signatures** | ML-DSA-65 (FIPS 204) | Signing User Hash IDs, Project Hash IDs, VCs, registry entries |
| **Source Tree Hashing** | BLAKE3 | Normalizing and fingerprinting codebases |
| **Composite Hashing** | SHA3-256 | Constructing composite ID hashes from multiple inputs |
| **Key Derivation** | HKDF-SHA3-256 | Deriving registration keys from identity material |
| **Authenticated Encryption** | ChaCha20-Poly1305 | Encrypted registry entries, private metadata |

All of these are post-quantum secure or quantum-neutral. OSSVI registrations are tamper-evident against both classical and quantum adversaries from genesis.

This is not incidental. NFTs are secured by ECDSA — broken by a sufficiently capable quantum computer. OSSVI Project Hash IDs are secured by ML-DSA-65. The difference matters on a 10-20 year horizon for any asset expected to carry long-term value.

### The OSSVI DID Method (`did:ossvi`)

OSSVI defines its own Decentralized Identifier method:

```
did:ossvi:<sha3-256(ml-dsa-65-signing-public-key)>
```

A `did:ossvi` identifier is:
- Self-issued — derived from key material the user generates
- Non-transferable — bound to the signing key; ownership cannot be sold or delegated
- Resolvable — the DID document is published to the OSSVI Registry Network
- Post-quantum — the underlying key material is ML-DSA-65

This is the User Hash ID primitive. Every participant in the OSSVI ecosystem holds exactly one `did:ossvi` identifier, tied to verified platform account ownership.

### Verifiable Credentials

OSSVI issues W3C-style Verifiable Credentials signed with ML-DSA-65 for:
- Project registration attestations
- Contributor relationship records
- License compliance proofs
- Ownership transfer events

VCs are the mechanism by which the protocol makes claims that can be verified by any third party without contacting OSSVI infrastructure.

---

## The OSSVI Registry Network

OSSVI does not run on an external blockchain. It runs on the **OSSVI Registry Network** — a distributed network of registry nodes that store, replicate, and verify signed registry entries.

### Why Not an External Chain

External L1/L2 chains introduce:
- Gas costs that make per-project and per-contributor registrations economically prohibitive at OSS scale
- Quantum vulnerability (all current production chains use ECDSA or similar)
- Governance dependencies on external protocol communities
- Performance bottlenecks for high-frequency registry reads
- Trust in a third-party consensus mechanism

The OSSVI Registry Network eliminates all of these by owning its own consensus and storage model, purpose-built for the registry use case.

### What the Registry Network Provides

The guarantee that blockchains are typically invoked to provide is: *tamper-evident, independently verifiable, append-only records with provable timestamps.* The OSSVI Registry Network provides this guarantee through a different mechanism:

- **Merkle forest of signed entries** — every registry entry is an ML-DSA-65 signed record; tampering invalidates the signature
- **Multi-node replication** — entries are replicated across independent registry nodes; no single point of failure or censorship
- **Content-addressed storage** — Project Hash IDs are cryptographically derived from content; the same project always produces the same ID
- **Timestamp anchoring** — entry timestamps are periodically anchored to external public records (existing blockchains, certificate transparency logs, or equivalent) for independent time-ordering proofs, without running state on those chains
- **DID-bound ownership** — a Project Hash ID is owned by the `did:ossvi` that registered it; ownership is cryptographically enforced, not contractually

### Registry Node Architecture

Registry nodes are lightweight. A node:
- Stores the registry manifest (all Project Hash IDs + metadata)
- Verifies ML-DSA-65 signatures on incoming registrations
- Replicates the manifest to peer nodes
- Serves read queries (project lookup, contributor verification, license status)
- Does not mine, stake, or run a virtual machine

Running a registry node should be achievable on commodity hardware. The network is designed so that any organization with a stake in OSS sustainability — foundations, companies, universities — can operate one.

### The OSSVI Protocol Is Also a PQC Identity Infrastructure

This is the emergent property worth naming explicitly: what OSSVI requires to function — `did:ossvi` identifiers, ML-DSA-65 signed VCs, a distributed registry of signed records, platform-verified human identity anchors — is, structurally, a post-quantum cryptographic identity and data sovereignty layer.

OSSVI does not *depend on* a broader PQC infrastructure existing. It *creates* one as a byproduct of solving the OSS value problem correctly. A broader ecosystem of PQC-native applications — private communication, data sovereignty, sovereign social spaces — can grow around the OSSVI primitive layer rather than requiring it to be built first and separately.

The narrative reads cleanly: OSSVI was built to give open source projects economic identity. It turned out to also be a quantum-resistant identity infrastructure. The PQC layer is a consequence, not a prerequisite.

---

## The OSSVI Concept

OSSVI combines five interlocking primitives:

1. **User Hash ID (`did:ossvi`)** — Every verified human participant receives a unique, cryptographically derived identity anchor derived from an ML-DSA-65 signing key. This is their wallet, reputation record, and ownership claim across all registered projects. Issued once, verified against platform accounts (GitHub, npm, etc.), and non-transferable.

2. **Project Hash ID** — Every registered project receives a unique hash derived from its normalized codebase, metadata, and owner identity. This hash is recorded in the OSSVI Registry Network and becomes the project's permanent asset. The Project Hash ID *is* the asset — analogous to a token ID but anchored to real utility, secured by post-quantum cryptography, requiring no gas. Initial valuation is assigned at registration and subsequently governed by registry dynamics. *(Initial valuation mechanism: see Pinned Question #2.)*

3. **Smart License Layer** — License terms (any existing SPDX license, or the OSSVI Genesis License) are encoded as signed VC records. Derivative works, forks, and commercial usages are detectable and governable. The OSSVI Genesis License explicitly encodes: AI systems are not legal persons, cannot own assets, and cannot hold or accrue value in the protocol except as agents acting on behalf of a verified human `did:ossvi`.

4. **Utility-Weighted Valuation** — The on-chain value of a Project Hash ID reflects the project's real utility to the ecosystem. High-influence, widely-depended-upon, ecosystem-critical projects are weighted more heavily. Trivial, redundant, or low-impact projects are weighted less, approaching zero. *(Full signal design: see Pinned Question #2.)*

5. **Value Capture & Distribution** — Once an asset has registry value, that value can be staked, commercially licensed, or traded, with distributions routed to the project's registered contributors via their `did:ossvi` identifiers. *(Payment mechanism: see Pinned Question #1. Contributor weighting: see Open Design Question #3.)*

### The Self-Referential Bootstrap

The OSSVI protocol itself will be the **first registered asset in its own registry** — its GitHub repository, licensed under the OSSVI Genesis License, issued Project Hash ID #1 at launch. This simultaneously:

- Proves the full stack works
- Establishes on-registry provenance for the entire protocol
- Creates an immutable timestamp of origin
- Anchors the genesis of the `did:ossvi` DID method to a real, verifiable event

---

## System Architecture

### Layer Stack

| Layer | Function | Technology |
|---|---|---|
| **OSSVI Primitive Layer** | ML-DSA-65 signatures, BLAKE3/SHA3-256 hashing, HKDF, ChaCha20-Poly1305 | OSSVI-native PQC substrate |
| **Identity: User** | Verified human identity → `did:ossvi` (wallet + ownership registry) | `did:ossvi` method, platform OAuth verification, ZK proof of humanity |
| **Identity: Project** | Codebase + metadata + owner → Project Hash ID (the asset) | BLAKE3 source tree hash, SHA3-256 composite, ML-DSA-65 VC |
| **Licensing** | Signed VC records encoding license terms, AI-ownership prohibition | ML-DSA-65 VCs, SPDX registry integration, OSSVI Genesis License |
| **Registry Network** | Distributed append-only registry of signed entries | Merkle forest, multi-node replication, timestamp anchoring |
| **Valuation** | On-registry scoring assigning and updating project value | Oracle network, protocol-governed weighting; initial mint value TBD *(Pinned #2)* |
| **Monetization** | Asset staking, commercial licensing, trading with royalty splits | DeFi-adjacent primitives or native OSSVI value exchange |
| **Anti-Abuse** | Bot detection, botnet prevention, sybil resistance | Rate limiting, platform cross-verification, behavioral analysis |
| **Governance** | Protocol-level decision making for upgrades and disputes | Hybrid foundation + on-registry contributor voting |

### `did:ossvi` User Hash ID Scheme

```
USER_ID = did:ossvi:<sha3-256(ml-dsa-65-signing-public-key)>
```

Full identity record constructed from:

```
USER_RECORD = ML-DSA-65-Sign(
  signing_key,
  {
    did: did:ossvi:<hash>,
    platform_identity_set,     // verified GitHub + npm + other platform account IDs
    registration_timestamp,    // registry timestamp at first registration
    registry_nonce,            // unique anti-collision nonce from protocol
    proof_of_humanity_signal   // ZK or oracle-attested human verification
  }
)
```

Properties:
- One `did:ossvi` per human. Non-transferable.
- Acts as the user's registry wallet — all owned Project Hash IDs are attached to it
- Ownership verified at registration by cross-referencing platform account ownership
- Human-only: AI systems cannot hold a `did:ossvi`. Agentic coding tools operating on behalf of a human act *through* that user's DID, not independently
- Independently verifiable by any third party with the public key

### Project Hash ID Scheme

```
PROJECT_ID = SHA3-256(
  BLAKE3(canonical_source_tree),     // normalized codebase fingerprint
  owner_did_ossvi,                   // anchors the project to its human owner
  license_identifier,                // SPDX ID or OSSVI Genesis License ID
  registration_timestamp,            // registry timestamp at registration
  registry_nonce                     // unique anti-collision nonce
)
```

The Project Hash ID is then signed by the owner's `did:ossvi` signing key and published as a VC to the OSSVI Registry Network.

Properties:
- One Project Hash ID per project. The ID is the asset.
- Forks produce new Project Hash IDs with a lineage pointer to the parent
- Independently reproducible and verifiable from source
- Post-quantum secure: ML-DSA-65 signature means the registration cannot be forged even by a quantum adversary

### Source Tree Normalization

The `BLAKE3(canonical_source_tree)` component requires a well-defined normalization algorithm to ensure reproducibility. Key design requirements:

- Exclude build artifacts, generated files, and binary dependencies
- Normalize line endings and whitespace consistently
- Exclude files specified in `.ossviignore` (analogous to `.gitignore`)
- Process the contributor-owned source deterministically
- Produce identical output from identical source across any environment

The normalization specification is a Phase 1 deliverable and is non-trivial. It must be formally specified before any registry implementation begins.

### Valuation Model — Current Direction

The Project Hash ID functions analogously to a token mint event. Initial value is assigned at registration time by the protocol itself. On-registry dynamics (staking, licensing, trading, adoption) then govern value evolution post-registration.

The key design requirement: high-influence / ecosystem-critical projects should receive meaningfully higher initial valuations than trivial or redundant projects. The gradient runs from "core Linux kernel dependencies" → "popular utility libraries" → "niche tools" → "one-off experiments" → effectively zero.

*(Full design workshopping: **Pinned Question #2**)*

---

## Anti-Bot & AI-Ownership Governance

### AI Ownership Prohibition

The OSSVI Genesis License and protocol rules explicitly establish:

- AI systems, agents, and bots are not legal persons and cannot own, register, or accrue value from OSSVI assets
- A Project Hash ID must be owned by a verified human `did:ossvi`
- Agentic coding tools may be used in project development without restriction — using AI to write code does not affect registrability
- The prohibition is on *autonomous AI ownership and value accrual*, not on *AI-assisted human development*

Encoded at the license and protocol layers, not just as policy.

### Sybil & Botnet Resistance

| Attack | Mitigation |
|---|---|
| Bot farm creating mass `did:ossvi` identifiers | Platform OAuth cross-verification; proof-of-humanity layer; registration rate limiting |
| Coordinated fake signal inflation to boost valuation | Signal design weights hard-to-fake signals; on-registry anomaly detection; stake-weighted reputation |
| Trivial project mass-registration to flood the registry | Minimum registration requirements; near-zero initial valuation for projects below threshold |
| Agentic bots acting on behalf of a human to game contribution weighting | All agentic actions attributed to the sponsoring human `did:ossvi`; rate limiting on contribution registrations per DID per time window |

---

## Design Principles

### Non-Negotiable

- **Opt-in only** — Existing MIT/Apache/GPL projects are not affected. Registration is voluntary. The open source grant of any existing license is never altered by OSSVI registration.
- **Human ownership only** — `did:ossvi` identifiers and Project Hash IDs are owned by verified humans.
- **Retroactive provenance** — Any project can register, including existing mature projects.
- **Forkability preserved** — Forks produce new Project Hash IDs with lineage links. Forking remains a first-class right.
- **Post-quantum from genesis** — All signatures are ML-DSA-65. No OSSVI asset can be forged by a quantum adversary.

### Strongly Preferred

- **Identity-first rollout** — `did:ossvi` and Project Hash ID registration provide standalone utility (namespace integrity, provenance timestamps, fork lineage) independent of the value layer. Lead with identity; let monetization follow adoption.
- **Gas-free by design** — The OSSVI Registry Network charges no transaction fees for registration. Cost is borne by the network's governance/sustainability model, not per-transaction.
- **License compatibility matrix** — The VC layer encodes SPDX license compatibility rules to govern derivative work licensing automatically.
- **Dual-mode design** — OSSVI wraps any existing license without replacing it. The value layer is additive.

---

## Known Challenges

### ⚠️ Pinned Question #1 — Value Capture / Payment Mechanism

*How does value actually enter the system from commercial users?*

OSSVI VCs can govern on-registry behavior between willing participants. They cannot compel a corporation to pay royalties for software it uses under an MIT license. The gap between "OSSVI assigns a value to a project" and "that value flows to maintainers" is the central unsolved design problem.

Deferred for dedicated workshopping. No architecture decisions dependent on this mechanism should be finalized until resolved.

### ⚠️ Pinned Question #2 — Initial Valuation & Signal Design

*What determines a Project Hash ID's initial on-registry value at mint, and how does it evolve?*

The model treats the Project Hash ID as a mint event. Initial value must reflect real ecosystem influence. The mechanism for computing this is unresolved — signal design, oracle architecture, and gaming resistance are all open.

### Open Design Question #3 — Contributor Model

Under active reconsideration. Leading candidate directions:

- **Flat owner-weighted** — Project Hash ID value accrues entirely to the registered owner's `did:ossvi`
- **Role-tiered** — Defined roles (core maintainer, significant contributor, occasional contributor) with fixed protocol-governed percentage splits
- **Governance-declared** — Project owner declares contributor splits at registration; protocol enforces the declared distribution

### Open Design Question #4 — Source Tree Normalization Specification

What constitutes the canonical source tree for hashing purposes? This must be formally specified before any implementation begins. Ambiguity here breaks reproducibility, which breaks the identity guarantee.

### Open Design Question #5 — Registry Node Incentive Model

What motivates entities to run registry nodes? Options: protocol treasury allocation, foundation grants, mutual interest (companies that depend on OSS have direct incentive), or a lightweight staking model. This is downstream of PIN-1 resolution.

### Community Trust

The OSS community is culturally allergic to financialization — and this objection is principled. OSSVI's architectural answer:

- Opt-in removes the enclosure objection
- Identity-first rollout demonstrates utility before monetization
- Human-only ownership and contributor-weighted distribution frames this as *creator value capture*, not speculation
- Governance non-transferability blocks plutocratic capture
- Gas-free operation removes the "crypto tax" objection
- Post-quantum security is a genuine technical advancement, not marketing

### Retroactivity & Ownership Ambiguity

An **OSSVI Contributor Agreement (OCA)** — analogous to a CLA but granting value participation rights — will likely be required for projects seeking full monetization features. Projects with ambiguous ownership can register for identity/provenance features without activating the value layer.

---

## Relationship to Project Aegis

Project Aegis (WCNegentropy/PQC-Layer-Project) is a post-quantum cryptographic platform for data sovereignty, private-node social infrastructure, and encrypted communications. OSSVI and Aegis share a conceptual substrate — both require PQC-native identity, verifiable credentials, and distributed registry infrastructure.

The relationship is deliberately structured as follows:

**OSSVI defines the primitives. Aegis may grow around them.**

OSSVI extracts the minimum PQC substrate it needs — `did:ossvi`, ML-DSA-65 VCs, the registry network — and owns that substrate. The broader Aegis ecosystem (vault, gateway, social runtime, private-node browser) is architecturally compatible with and may interoperate with OSSVI primitives, but OSSVI does not depend on Aegis shipping first.

This inverts a dependency risk: OSSVI can ship independently. If a broader PQC data sovereignty ecosystem emerges around the OSSVI primitive layer, that is a compounding benefit, not a prerequisite.

From the outside, the story reads: OSSVI was built to solve OSS sustainability. It turned out to create a quantum-resistant identity and registry infrastructure as a byproduct. The PQC layer is a consequence of doing OSSVI correctly, not a dependency imported from elsewhere.

---

## Differentiation from Prior Art

| System | What It Does | What It Lacks |
|---|---|---|
| **FOSS-chain** (2025) | Blockchain + smart contracts for OSS license compliance, function-level SHA-256 hashing | No economic value layer; no asset valuation; not PQC-native |
| **Tokenized OSS Licenses** (concept) | Licensing rights as blockchain tokens for traceability | No utility-anchored valuation; quantum-vulnerable |
| **Gitcoin** | Crypto-based OSS funding via grants | Donation-based, not asset-based; no project identity layer |
| **NFTs** | Unique on-chain digital asset ownership | Purely speculative; quantum-vulnerable; no licensing layer |
| **Open Core / SaaS** | Monetization via derivative commercial products | Treats project as service vehicle, not first-class asset |
| **ORCID / DOI** | Persistent identity for academic works and authors | Identity only; no value layer; not PQC-native |
| **Project Aegis** | PQC data sovereignty, private-node social infrastructure | Complementary; not competing; may share OSSVI primitives |
| **OSSVI** | `did:ossvi` identity + PQC-native registry + licensing + utility valuation + value distribution + bot governance | *The full stack — post-quantum from genesis, gas-free, human-owned* |

---

## Governance

**Recommended v1 approach:** A lightweight founding foundation operating the registry infrastructure and protocol, with on-registry contributor voting for protocol parameter changes (signal weights, fee schedules, license registry updates). Governance rights are **non-transferable** and tied to verified contribution records — not purchasable.

The founding foundation is temporary scaffolding, not a permanent power structure. The goal is a self-governing protocol where the foundation's role diminishes as the contributor governance layer matures.

---

## Roadmap

### Phase 0 — Concept & Provenance *(Active — Now → Q3 2026)*
- [x] Concept document v0.1 — May 27, 2026
- [x] Private GitHub repository established — OSSVI repo, May 27, 2026
- [x] Concept document v0.1.1 — User Hash ID, bot governance, architecture refinements
- [x] Concept document v0.1.2 — OSSVI Primitive Layer, `did:ossvi`, OSSVI Registry Network, Aegis relationship inverted
- [ ] Workshopping sessions: Pinned Question #1 (Payment Mechanism), Pinned Question #2 (Valuation/Signals)
- [ ] Contributor model decision (ODQ-3)
- [ ] Source tree normalization specification (ODQ-4)
- [ ] OSSVI Genesis License — draft and review
- [ ] Registry node incentive model (ODQ-5)

### Phase 1 — Protocol Design *(Q3 → Q4 2026)*
- [ ] Formal specification of the full layer stack
- [ ] `did:ossvi` method specification — complete DID resolution, document format, update/revocation rules
- [ ] Source tree normalization algorithm — formal spec, reference implementation
- [ ] Project Hash ID specification — composite construction, VC schema, registry entry format
- [ ] OSSVI Registry Network specification — node protocol, replication model, consensus approach
- [ ] Valuation oracle design and initial mint mechanism *(after PIN-2 resolution)*
- [ ] ML-DSA-65 VC schema definitions for all credential types
- [ ] Anti-abuse framework specification
- [ ] SPDX license compatibility matrix encoding plan

### Phase 2 — Reference Implementation *(Q4 2026 → Q1 2027)*
- [ ] OSSVI Primitive Layer — Rust library (ML-DSA-65, BLAKE3, SHA3-256, HKDF, ChaCha20-Poly1305)
- [ ] `did:ossvi` — DID generation, document publication, resolution
- [ ] CLI tool for project registration and Project Hash ID generation
- [ ] Registry node reference implementation
- [ ] Self-registration: OSSVI repo issues its own Project Hash ID #1
- [ ] Platform verification flow (GitHub/npm OAuth integration)
- [ ] Valuation oracle prototype *(after PIN-2 resolution)*

### Phase 3 — Private Beta & Hardening *(Q1 → Q2 2027)*
- [ ] Invite cohort of OSS maintainers for private beta
- [ ] Security audit of primitive layer and registry node implementation
- [ ] Anti-abuse stress testing
- [ ] Governance framework finalization
- [ ] Legal review of OSSVI Contributor Agreement (OCA)
- [ ] Registry network multi-node stress testing

### Phase 4 — Public Launch *(Q2 2027+)*
- [ ] Public registry launch
- [ ] OSSVI Genesis Asset goes live as Project Hash ID #1
- [ ] OSI/SPDX outreach and standards coordination
- [ ] Developer documentation and SDK release
- [ ] Registry node onboarding for foundation partners and major OSS stakeholders

---

## Open Questions Summary

### Pinned (Dedicated Workshopping Required)
- **[PIN-1]** Value capture / payment mechanism: who pays, when, triggered by what, enforced how?
- **[PIN-2]** Initial on-registry valuation at Project Hash ID mint: signals, weights, oracle, gaming resistance?

### Active Design Questions
- **[ODQ-3]** Contributor model: flat owner-weighted, role-tiered, or governance-declared?
- **[ODQ-4]** Source tree normalization: full specification of what constitutes canonical source
- **[ODQ-5]** Registry node incentive model: what motivates independent node operation?
- **[ODQ-6]** Forks and parent value: does a fork inherit any fraction of parent asset value?
- **[ODQ-7]** Corporate CLA projects: how are projects with upstream IP assignments (Google, Red Hat, etc.) handled?
- **[ODQ-8]** Governance token: purely governance-scoped, unit of account, or both? *(Downstream of PIN-1)*
- **[ODQ-9]** Minimum registration requirements: floor preventing trivial project flooding without excluding legitimate small projects?

---

## Provenance Declaration

This document was created on **May 27, 2026** by Mikeal Clark as the evolving concept record for OSSVI. Version 0.1 was the initial concept. Version 0.1.1 refined the primitive architecture. Version 0.1.2 resolves the chain selection question by defining OSSVI as its own PQC-native registry protocol, establishes the `did:ossvi` DID method, defines the OSSVI Primitive Layer, and inverts the relationship with Project Aegis — OSSVI defines the substrate; a broader ecosystem grows around it. The OSSVI project itself will, upon launch, be registered as Project Hash ID #1 in its own registry.

---

*OSSVI Concept Document v0.1.2 — Private / Pre-Release — All rights reserved pending licensing decision*
