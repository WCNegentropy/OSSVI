# OSSVI — Open Source Value Infrastructure
### Concept Document v0.1.3 | Private Draft | May 27, 2026

> **Status:** Pre-development concept. This document exists to establish provenance and provide a planning foundation. Not for public release.
>
> **Changelog from v0.1.2:** Resolved ODQ-4 (source tree normalization) — git initial commit tree hash is the canonical project identity anchor; per-platform equivalents defined for non-git registries. Introduced the two-tier identity model separating project owner registration from contributor registration. Added full Valuation Architecture section incorporating Markov regime modeling primitives from quantitative finance: cross-project corpus transition matrix, n-step forward probabilities, and P(growing) - P(declining) as the scalar valuation signal. PIN-2 formally decomposed into the shape problem (partially resolved by Markov framework) and the anchor problem (still open). Bootstrapping requirement for pre-populated corpus added to roadmap.

---

## Overview

OSSVI (Open Source Value Infrastructure) is a proposed protocol-layer system that gives open source software projects a cryptographically unique, immutable identity — and couples that identity to a structured value and monetization framework. It is designed to finally close the gap between the enormous economic value that open source generates for the world and the near-zero value that flows back to the creators and maintainers of that work.

The core insight: an open source project's codebase is a real, utility-bearing asset. OSSVI treats it as such — not speculatively like an NFT, but with value derived from measurable, forward-looking ecosystem signals. The Project Hash ID *is* the asset. Initial valuation is determined by the project's expected trajectory relative to the corpus of all registered projects — not by arbitrary assignment or pure speculation.

**What NFTs should have been.** Unique identity anchored to something real, owned by the people who built it, quantum-resistant from day one, valued by forward-looking probability rather than market sentiment, no gas fees.

OSSVI is also, incidentally, the foundation of a post-quantum cryptographic identity and registry infrastructure. That is not the pitch — it is an emergent consequence of doing the protocol correctly.

---

## Problem Statement

Open source software powers an estimated **$8.8 trillion in economic value** annually yet the developers and maintainers behind it capture almost none of it. Existing monetization strategies — open core, SaaS wrappers, dual licensing, donations, sponsorships — all treat the project as a *service vehicle* rather than a *first-class asset*. None provide:

- A persistent, tamper-proof identity for a codebase anchored to its origin
- A mechanism for the work itself (not a derivative service) to carry transferable economic value
- Automatic, transparent value distribution across verified contributors proportional to foundational contribution
- A forward-looking, gaming-resistant valuation mechanism grounded in ecosystem trajectory
- License compliance enforcement that scales across the full OSS dependency graph
- Protection against bot-mediated gaming of the system

The result is a structurally broken economy where trillion-dollar infrastructure is built on volunteer labor with no systemic value-return mechanism.

---

## The OSSVI Primitive Layer

OSSVI defines its own minimal post-quantum cryptographic substrate. This is not a dependency on any external project — it is the lowest layer of the protocol.

### Cryptographic Primitives

| Primitive | Algorithm | Purpose |
|---|---|---|
| **Digital Signatures** | ML-DSA-65 (FIPS 204) | Signing User Hash IDs, Project Hash IDs, VCs, registry entries |
| **Source Tree Hashing** | BLAKE3 | Re-hashing git tree content and non-git source artifacts |
| **Composite Hashing** | SHA3-256 | Constructing composite ID hashes from multiple inputs |
| **Key Derivation** | HKDF-SHA3-256 | Deriving registration keys from identity material |
| **Authenticated Encryption** | ChaCha20-Poly1305 | Encrypted registry entries, private metadata |

All primitives are post-quantum secure or quantum-neutral. OSSVI registrations are tamper-evident against both classical and quantum adversaries from genesis. This is not incidental — NFTs are secured by ECDSA, which a sufficiently capable quantum computer breaks. OSSVI Project Hash IDs are secured by ML-DSA-65. The difference matters on any horizon where these assets are expected to carry long-term value.

**Note on git SHA-1:** Git currently uses SHA-1 for tree and commit hashes. SHA-1 is not part of the OSSVI security model. Git tree hashes are used as *input* to OSSVI's BLAKE3/SHA3-256 construction, not as the final identity anchor. The security guarantee comes from OSSVI's own hashing layer, not git's.

### The OSSVI DID Method (`did:ossvi`)

```
did:ossvi:<sha3-256(ml-dsa-65-signing-public-key)>
```

A `did:ossvi` identifier is self-issued, non-transferable, bound to ML-DSA-65 key material, and independently verifiable by any third party. This is the User Hash ID primitive.

---

## Identity Architecture — Two-Tier Model

OSSVI uses a strictly separated two-tier identity model. The two tiers are distinct registration events with distinct cryptographic structures and distinct economic roles.

### Tier 1 — Owner Registration (Project Hash ID under Creator's User Hash ID)

When a project owner registers their project, the system produces a **Project Hash ID** that is cryptographically anchored to:

1. The project's genesis artifact (see Source Identity Anchoring below)
2. The owner's `did:ossvi` User Hash ID
3. The OSSVI Genesis License or declared SPDX license identifier
4. The registration timestamp
5. A unique registry nonce

The owner is defined as the `did:ossvi` holder under which the Project Hash ID was originally registered. This is the canonical ownership record — it cannot be disputed because it is cryptographically derived from the owner's key material.

### Tier 2 — Contributor Registration (Project Hash ID under Contributor's User Hash ID)

When a contributor registers their relationship to an existing project, the system produces a **Contributor Stake Record** — a signed VC that references the Project Hash ID but is anchored to the contributor's own `did:ossvi`.

The system distinguishes contributors from owners by a simple structural check:

```
IF project_hash_id.owner_did == contributor_did:
    → This is the project owner
ELSE IF project_hash_id exists in registry AND contributor_did has verified commits:
    → This is a contributor; issue Contributor Stake Record
```

This separation is intentional and important. Owner registration and contributor registration are independent events. A contributor's stake record is their own signed VC — the project owner cannot unilaterally revoke it after issuance. The contributor holds their own proof of contribution.

### Economic Analogy

The two-tier model maps to a token economics structure:

- **Owner Registration** = Genesis mint event. The project enters the registry with an initial valuation (see Valuation Architecture). This is analogous to a token's initial issuance or market cap at genesis.
- **Contributor Stake Records** = Distribution of that supply across contributors. Initial commits represent foundational work and receive proportionally larger stakes — directionally in the range of 5-20% of total project value per foundational contributor, though the exact mechanism requires workshopping. Subsequent contributors receive proportionally smaller stakes reflecting incremental contribution.
- **Post-registration ecosystem activity** = Ongoing valuation dynamics. As the project's ecosystem signals evolve, its on-registry value updates — analogous to price discovery after initial issuance.

The contributor model is still under active design (see ODQ-3). The key structural point established here is that contributor stakes are computed *over* the initial project valuation, not independently of it.

---

## Source Identity Anchoring

ODQ-4 is resolved for git-based projects. Non-git platforms require per-platform equivalents defined below.

### Git-Based Projects (Primary Case)

The canonical source identity anchor for a git-based project is the **initial commit tree hash**, re-hashed through BLAKE3:

```
SOURCE_ANCHOR = BLAKE3(
  git_tree_hash_at_initial_commit,   // content-addressed, immutable, reproducible
  repository_canonical_url,          // github.com/owner/repo or equivalent
  initial_commit_timestamp           // UTC timestamp of the first commit
)
```

**Why the initial commit:**

The initial commit tree hash is already content-addressed and immutable — it predates any contribution and any tampering. It is reproducible by any third party with access to the repository. It does not change as the project evolves. Git already solved the canonical source tree problem; OSSVI inherits that solution and re-hashes it through its own security layer to eliminate any dependence on git's SHA-1.

The initial commit is a pure identity anchor, not a quality judgment. A project whose initial commit was a scaffolded empty repo with one README has the same structural anchor as one whose initial commit was a complete working system. The anchor establishes *when and what* the project began — valuation is handled separately by the Valuation Architecture.

**Normalization rules for git projects:**
- Use the first commit in topological order (root of the DAG)
- For repositories with multiple root commits (history-rewritten repos), use the earliest by timestamp
- Exclude `.gitignore`-listed files from the tree hash input per standard git behavior
- Re-hash through BLAKE3 regardless of git's internal hash algorithm (SHA-1 or SHA-256)

### Non-Git Platform Equivalents

| Platform | Genesis Artifact | Equivalent Anchor |
|---|---|---|
| **npm** | Initial published version tarball + package.json | BLAKE3(tarball_sha512 + package_name + initial_publish_timestamp) |
| **PyPI** | Initial sdist/wheel + PKG-INFO | BLAKE3(dist_sha256 + project_name + initial_upload_timestamp) |
| **crates.io** | Initial crate version + Cargo.toml | BLAKE3(crate_sha256 + crate_name + initial_publish_timestamp) |
| **RubyGems** | Initial gem package + gemspec | BLAKE3(gem_sha256 + gem_name + initial_push_timestamp) |
| **Maven Central** | Initial artifact JAR + POM | BLAKE3(artifact_sha512 + group_artifact_id + initial_deploy_timestamp) |
| **No package registry** | Self-declared source archive | BLAKE3(archive_sha256 + declared_url + registration_timestamp) + manual verification requirement |

The principle across all platforms: find the immutable root artifact representing the project's genesis, use its content hash plus stable metadata plus timestamp as the anchor input, and re-hash through BLAKE3. For projects with both a git history and a package registry presence, the git initial commit takes precedence as the more fundamental anchor.

---

## Project Hash ID Construction

```
PROJECT_ID = SHA3-256(
  SOURCE_ANCHOR,                     // platform-specific genesis artifact hash (BLAKE3)
  owner_did_ossvi,                   // did:ossvi of the registering owner
  license_identifier,                // SPDX ID or "ossvi-genesis-v1"
  registration_timestamp,            // OSSVI registry timestamp
  registry_nonce                     // unique anti-collision nonce from protocol
)
```

The Project Hash ID is then signed by the owner's `did:ossvi` ML-DSA-65 signing key and published as a VC to the OSSVI Registry Network. The signature makes ownership cryptographically non-repudiable.

Forks produce new Project Hash IDs with a `parent_project_id` lineage pointer. A fork has its own independent identity, valuation, and contributor graph — it does not inherit the parent's asset value, though lineage is permanently traceable.

---

## Valuation Architecture

This section addresses the shape of the initial valuation mechanism. It does not yet specify the absolute value anchor (see PIN-2 decomposition below).

The core insight borrowed from quantitative regime modeling: **project ecosystem health exhibits state-transition behavior that can be modeled probabilistically.** The question "what is this project worth at registration?" is reframed as "what is this project's expected ecosystem trajectory relative to the corpus of all registered projects, discounted by time horizon?"

### Project Ecosystem States

Three states are defined for the v1 model:

| State | Definition |
|---|---|
| **Growing** | Expanding downstream dependents, active commit cadence, increasing download velocity, positive contributor trend |
| **Stable** | Large established dependent base, maintained but low-activity, flat or slowly growing signals |
| **Declining** | Stagnating or shrinking dependents, dormant commits, security debt accumulating, contributor attrition |

These thresholds are provisional and subject to governance. The Hidden Markov Model extension (Phase 2) removes the need for fixed thresholds by discovering natural state boundaries from the data — which also closes the gaming surface that fixed thresholds create.

### The Corpus Transition Matrix

The transition matrix is built across **the entire OSSVI registered project corpus**, not per-project. Every registered project contributes its state transition history to the aggregate matrix:

```
T = | P(G→G)  P(G→S)  P(G→D) |
    | P(S→G)  P(S→S)  P(S→D) |
    | P(D→G)  P(D→S)  P(D→D) |
```

Where each cell is the observed probability of transitioning from one state (rows) to another (columns) across the corpus, measured over a standard period (monthly is the candidate cadence for v1).

**Why corpus-wide rather than per-project:** A single project's transition history is too sparse to be statistically reliable. The corpus-level matrix captures how open source project health *tends to evolve in general* — which is exactly the prior needed to evaluate a new registration.

**Stickiness:** The diagonal cells (P(G→G), P(S→S), P(D→D)) measure how persistent each state is. High stickiness in the Growing state means "once a project gets traction, it tends to keep it" — which is meaningful information for valuation. Low stickiness in the Declining state might indicate recovery is possible. These diagonal values are first-order valuation signals.

### N-Step Forward Probabilities

The n-step transition matrix T^n gives the probability distribution over states n periods into the future, starting from a known current state:

```
T^6  = 6-month forward probability distribution
T^12 = 12-month forward probability distribution
T^24 = 24-month forward probability distribution
```

For a project registering in state S_0, T^n × S_0 gives the probability vector [P(Growing at n), P(Stable at n), P(Declining at n)].

Long-horizon predictions (T^n for large n) converge to the stationary distribution — the long-run average distribution of states across the corpus. This convergence is expected and useful: it means the stationary distribution is the neutral baseline against which all valuations are calibrated. A project with a forward distribution significantly above the stationary baseline carries a premium; one that matches it is average; one below approaches zero.

### The Valuation Signal

The scalar valuation signal at horizon n is:

```
V(n) = P(Growing at n) - P(Declining at n)
```

This produces a value between -1 and +1. Positive and large means strong forward-looking health. Near zero means undifferentiated from the ecosystem baseline. Negative is a low-value or rejected registration.

The composite initial valuation signal is a time-discounted weighted sum across horizons:

```
V_initial = Σ [ discount(n) × V(n) ]
            for n in {6mo, 12mo, 24mo}
```

Where discount(n) applies a standard time-preference discount to future probabilities. This produces a single scalar that encodes the project's expected trajectory, calibrated against the corpus, weighted by time.

**Gaming resistance:** Because the valuation signal is derived from probability distributions over the corpus — not from raw signal counts on the individual project — it is significantly harder to manipulate than direct signal metrics. Inflating a project's download count moves one input signal; it does not move the corpus-wide transition matrix in any meaningful way. An attacker would need to manipulate the behavior of a large fraction of registered projects to shift the matrix materially.

### Bootstrapping Requirement

The transition matrix requires a statistically meaningful corpus before it is reliable. At launch with zero registrations, the matrix is empty. This is a hard constraint:

**OSSVI must pre-populate the corpus from historical open source project data before going live.**

Sources: GitHub Archive, Libraries.io, deps.dev, npm download history, PyPI stats, crates.io API. This pre-population assigns retroactive state histories to a large set of historical projects, trains the initial transition matrix, and establishes the stationary distribution baseline. The registry then launches with a trained prior rather than a cold-start problem.

Pre-population is a Phase 2 engineering deliverable and a prerequisite for Phase 4 (public launch).

### PIN-2 Decomposition

PIN-2 (initial valuation mechanism) is now partially resolved. The problem decomposes into two distinct sub-problems:

**Sub-problem A — Valuation Shape (partially resolved):**
*How is the relative valuation structure computed?*

The Markov corpus framework answers this. A project's initial valuation signal is its time-discounted P(Growing) - P(Declining) forward probability relative to the stationary distribution. The shape of the valuation mechanism is defined. Remaining work: finalizing state definitions, measurement cadence, horizon weights, and discount factors.

**Sub-problem B — Value Anchor (still open, PIN-2 remainder):**
*What does a valuation signal of +0.6 actually mean in absolute value units?*

The regime model tells you "this project's expected trajectory is 1.8× above the corpus average." It does not tell you what the corpus average is worth in dollar terms, token units, or any other absolute measure. This anchor — the mapping from relative valuation signal to absolute asset value — remains the core unresolved problem.

Options to workshop for the anchor: protocol treasury allocation at genesis, demand-side pricing from commercial licensees, a governance-set base unit updated periodically, or emergence from the trading/staking market once the ecosystem has sufficient liquidity. This remains PIN-2.

### Phase 2 Extension — Hidden Markov Model

The v1 model uses fixed state thresholds (the definitions of Growing/Stable/Declining above). This creates a known gaming surface: if thresholds are public and fixed, projects optimize to be just above the threshold, not to genuinely improve.

The Hidden Markov Model extension removes fixed thresholds by treating states as latent variables that the model infers from observable signals, rather than rules that humans specify. The model discovers natural clusters in project signal space — what "growing" actually looks like statistically — without being told where the boundaries are.

This is architecturally identical to the HMM extension in quantitative regime modeling, where it removes the arbitrary 5% price threshold by letting the model discover true market regimes. For OSSVI it serves the same purpose: state boundaries become a moving, data-driven target rather than a fixed gaming surface.

HMM is Phase 2 work. The v1 fixed-threshold model is sufficient for launch and provides a functional valuation mechanism while the corpus accumulates enough data for HMM training.

---

## The OSSVI Registry Network

OSSVI does not run on an external blockchain. It runs on the **OSSVI Registry Network** — a distributed network of registry nodes that store, replicate, and verify signed registry entries.

### Why Not an External Chain

External L1/L2 chains introduce gas costs prohibitive at OSS scale, quantum vulnerability (all current production chains use ECDSA or equivalent), external governance dependencies, and trust in a third-party consensus mechanism. The OSSVI Registry Network eliminates all of these by owning its own consensus and storage model, purpose-built for the registry use case.

### Tamper-Evidence Without Blockchain

The guarantee that blockchains are invoked to provide — tamper-evident, independently verifiable, append-only records with provable timestamps — is provided through:

- **Merkle forest of ML-DSA-65 signed entries** — tampering invalidates signatures; any third party can verify
- **Multi-node replication** — entries replicated across independent registry nodes; no single point of failure
- **Content-addressed Project Hash IDs** — the same project always produces the same ID; fabrication is detectable
- **Timestamp anchoring** — entry timestamps periodically anchored to external public records (existing blockchains or certificate transparency logs) for independent time-ordering proofs, without running state on those chains
- **DID-bound ownership** — Project Hash IDs are cryptographically bound to the `did:ossvi` that registered them

### Registry Node Architecture

Registry nodes are lightweight. A node stores the registry manifest, verifies ML-DSA-65 signatures on incoming registrations, replicates the manifest to peers, and serves read queries. It does not mine, stake, or run a virtual machine. Running a registry node should be achievable on commodity hardware — designed so that any organization with a stake in OSS sustainability can operate one.

---

## Anti-Bot & AI-Ownership Governance

### AI Ownership Prohibition

Encoded at the license and protocol layers:

- AI systems, agents, and bots cannot own, register, or accrue value from OSSVI assets
- A Project Hash ID must be owned by a verified human `did:ossvi`
- Agentic coding tools may be used in development without restriction — using AI to write code does not affect registrability
- The prohibition is on *autonomous AI ownership and value accrual*, not *AI-assisted human development*

### Sybil & Botnet Resistance

| Attack | Mitigation |
|---|---|
| Bot farm creating mass `did:ossvi` identifiers | Platform OAuth cross-verification; proof-of-humanity layer; registration rate limiting |
| Coordinated signal inflation to boost valuation | Corpus-level transition matrix is resistant to single-project signal manipulation; anomaly detection; stake-weighted reputation |
| Trivial project mass-registration | Minimum registration requirements; near-zero valuation for projects below observable signal floor |
| Agentic bots gaming contribution weighting | All agentic actions attributed to sponsoring human `did:ossvi`; rate limiting per DID per time window |
| Gaming fixed state thresholds | HMM Phase 2 removes fixed thresholds entirely; v1 thresholds governed and updateable |

---

## Design Principles

### Non-Negotiable

- **Opt-in only** — Existing MIT/Apache/GPL projects are not affected. Registration is voluntary. No existing license is altered by OSSVI registration.
- **Human ownership only** — `did:ossvi` identifiers and Project Hash IDs are owned by verified humans.
- **Retroactive provenance** — Any project can register, including existing mature projects.
- **Forkability preserved** — Forks produce new Project Hash IDs with lineage links. Forking remains a first-class right.
- **Post-quantum from genesis** — All signatures are ML-DSA-65. No OSSVI asset can be forged by a quantum adversary.
- **Gas-free** — No transaction fees for registration or contributor stake issuance.

### Strongly Preferred

- **Identity-first rollout** — The `did:ossvi` and Project Hash ID layers provide standalone utility independent of the value layer. Lead with identity; let monetization follow adoption.
- **Corpus-grounded valuation** — Initial valuations are relative to the ecosystem, not absolute or arbitrary. No project is assigned a value that isn't grounded in its observable trajectory.
- **Gaming-resistant by architecture** — Valuation derived from corpus probability distributions, not raw signal counts.

---

## Known Challenges

### ⚠️ Pinned Question #1 — Value Capture / Payment Mechanism

*How does value actually enter the system from commercial users?*

OSSVI VCs govern on-registry behavior between willing participants. They cannot compel a corporation to pay royalties for software it uses under an MIT license. The gap between "OSSVI assigns a value to a project" and "that value flows to maintainers" is the central unsolved design problem. Deferred for dedicated workshopping.

### ⚠️ Pinned Question #2 (Remainder) — Absolute Value Anchor

*What does a valuation signal of +0.6 actually mean in absolute value units?*

The Markov framework resolves the shape of the valuation mechanism. The anchor — mapping from relative signal to absolute asset value — remains open. Options to workshop: protocol treasury genesis allocation, commercial licensee demand pricing, governance-set base unit, or market emergence from the trading/staking layer.

### Open Design Question #3 — Contributor Weighting Mechanism

Foundational contributors (initial commits, early core work) receive proportionally larger stakes — directionally 5-20% of total project value per foundational contributor. The exact weighting mechanism is under active design. Key tension: weighting must reward genuine early contribution without creating an incentive to front-load a massive "initial commit" to capture a large stake before adding collaborators. The weighting function needs to account for both the *timing* and the *significance* of contributions, not just timing alone.

### Open Design Question #5 — Registry Node Incentive Model

What motivates independent entities to run registry nodes? Options: protocol treasury allocation, foundation grants, mutual interest (organizations depending on OSS have direct incentive), or lightweight staking. Downstream of PIN-1.

### Open Design Question #6 — Fork Value Inheritance

Does a fork inherit any fraction of parent asset value at fork time? If yes, what fraction and under what conditions?

### Open Design Question #7 — Corporate CLA Projects

How are projects with upstream IP assignments (Google, Red Hat, Apache Foundation contributors) handled when the contributor signed away rights to a corporate entity?

### Open Design Question #8 — Governance Token Design

Purely governance-scoped, unit of account, or both? Downstream of PIN-1.

### Open Design Question #9 — Minimum Registration Requirements

What floor prevents trivial project flooding without excluding legitimate small or new projects?

### Community Trust

The OSS community is culturally allergic to financialization and the objection is principled. OSSVI's architectural answers: opt-in removes the enclosure objection; identity-first rollout demonstrates utility before monetization; human-only ownership frames this as creator value capture not speculation; gas-free operation removes the "crypto tax" objection; corpus-grounded valuation demonstrates that values aren't invented; post-quantum security is a genuine technical advancement.

---

## Relationship to Project Aegis

Project Aegis (WCNegentropy/PQC-Layer-Project) is a post-quantum cryptographic platform for data sovereignty, private-node social infrastructure, and encrypted communications. OSSVI and Aegis share a conceptual substrate — both require PQC-native identity, verifiable credentials, and distributed registry infrastructure.

The relationship is deliberately structured as:

**OSSVI defines the primitives. Aegis may grow around them.**

OSSVI extracts the minimum PQC substrate it needs — `did:ossvi`, ML-DSA-65 VCs, the OSSVI Registry Network — and owns that substrate independently. Aegis is architecturally compatible and may interoperate with OSSVI primitives, but OSSVI does not depend on Aegis shipping first.

The narrative reads: OSSVI was built to solve OSS sustainability. It turned out to create a quantum-resistant identity and registry infrastructure as a byproduct. The PQC layer is a consequence of doing OSSVI correctly, not a prerequisite imported from elsewhere.

---

## Differentiation from Prior Art

| System | What It Does | What It Lacks |
|---|---|---|
| **FOSS-chain** (2025) | Blockchain + smart contracts for OSS license compliance, SHA-256 hashing | No economic value layer; no trajectory-based valuation; not PQC-native |
| **Tokenized OSS Licenses** | Licensing rights as blockchain tokens | No utility-anchored valuation; quantum-vulnerable |
| **Gitcoin** | Crypto-based OSS funding via grants | Donation-based, not asset-based; no project identity layer |
| **NFTs** | Unique on-chain digital asset ownership | Purely speculative; quantum-vulnerable; no licensing layer; no trajectory valuation |
| **Open Core / SaaS** | Monetization via derivative commercial products | Treats project as service vehicle, not first-class asset |
| **ORCID / DOI** | Persistent identity for academic works and authors | Identity only; no value layer; not PQC-native |
| **Project Aegis** | PQC data sovereignty, private-node social infrastructure | Complementary; shares primitive substrate; not competing |
| **OSSVI** | Git-anchored identity + `did:ossvi` + PQC-native registry + corpus-grounded trajectory valuation + contributor stake model + bot governance | *The full stack — post-quantum from genesis, gas-free, trajectory-valued, human-owned* |

---

## Governance

**Recommended v1 approach:** A lightweight founding foundation operating the registry infrastructure and protocol, with on-registry contributor voting for protocol parameter changes (signal weights, valuation horizon discounts, state thresholds, license registry updates). Governance rights are **non-transferable** and tied to verified contribution records — not purchasable. The founding foundation is temporary scaffolding, not a permanent power structure.

---

## Roadmap

### Phase 0 — Concept & Provenance *(Active — Now → Q3 2026)*
- [x] Concept document v0.1 — May 27, 2026
- [x] Private GitHub repository established
- [x] Concept document v0.1.1 — User Hash ID, bot governance, architecture
- [x] Concept document v0.1.2 — OSSVI Primitive Layer, `did:ossvi`, Registry Network, Aegis relationship inverted
- [x] Concept document v0.1.3 — Git-anchored identity, two-tier contributor model, Markov valuation architecture, PIN-2 decomposed
- [ ] Workshopping: PIN-1 (Payment Mechanism)
- [ ] Workshopping: PIN-2 Remainder (Absolute Value Anchor)
- [ ] Contributor weighting mechanism (ODQ-3)
- [ ] OSSVI Genesis License — draft and review
- [ ] Registry node incentive model (ODQ-5)

### Phase 1 — Protocol Design *(Q3 → Q4 2026)*
- [ ] `did:ossvi` method specification — full DID resolution, document format, update/revocation
- [ ] Project Hash ID specification — complete construction, VC schema, registry entry format
- [ ] Source anchor specification — normalization rules per platform, git edge cases
- [ ] Contributor Stake Record VC schema
- [ ] OSSVI Registry Network specification — node protocol, replication, consensus model
- [ ] Valuation Architecture specification — state definitions, measurement cadence, horizon weights, discount factors
- [ ] Corpus pre-population plan — data sources, retroactive state labeling methodology
- [ ] Anti-abuse framework specification
- [ ] SPDX license compatibility matrix

### Phase 2 — Reference Implementation *(Q4 2026 → Q1 2027)*
- [ ] OSSVI Primitive Layer — Rust library (ML-DSA-65, BLAKE3, SHA3-256, HKDF, ChaCha20-Poly1305)
- [ ] `did:ossvi` — generation, document publication, resolution
- [ ] CLI tool — project registration, Project Hash ID generation, contributor stake issuance
- [ ] Registry node reference implementation
- [ ] Corpus pre-population pipeline — historical project state labeling, initial transition matrix
- [ ] Self-registration: OSSVI repo issues Project Hash ID #1
- [ ] Platform verification flow (GitHub/npm OAuth integration)
- [ ] Valuation oracle v1 (fixed-threshold Markov model)

### Phase 3 — Private Beta & Hardening *(Q1 → Q2 2027)*
- [ ] Invite cohort of OSS maintainers for private beta
- [ ] Security audit of primitive layer and registry node implementation
- [ ] Valuation model validation via walk-forward testing on historical project data
- [ ] Anti-abuse stress testing
- [ ] Governance framework finalization
- [ ] Legal review of OSSVI Contributor Agreement (OCA)
- [ ] Registry network multi-node stress testing
- [ ] HMM state discovery prototype (Phase 2 valuation extension)

### Phase 4 — Public Launch *(Q2 2027+)*
- [ ] Public registry launch with pre-populated corpus
- [ ] OSSVI Genesis Asset — Project Hash ID #1
- [ ] OSI/SPDX outreach and standards coordination
- [ ] Developer documentation and SDK release
- [ ] Registry node onboarding for foundation partners and major OSS stakeholders
- [ ] HMM transition (removes fixed state thresholds, closes gaming surface)

---

## Open Questions Summary

### Pinned (Dedicated Workshopping Required)
- **[PIN-1]** Value capture / payment mechanism: who pays, when, triggered by what, enforced how?
- **[PIN-2]** Absolute value anchor: what does a valuation signal of +0.6 mean in absolute value units?

### Active Design Questions
- **[ODQ-3]** Contributor weighting: how are foundational vs. incremental contributions differentiated beyond timing alone?
- **[ODQ-5]** Registry node incentive model: what motivates independent node operation?
- **[ODQ-6]** Fork value inheritance: does a fork inherit any fraction of parent asset value?
- **[ODQ-7]** Corporate CLA projects: how are projects with upstream IP assignments handled?
- **[ODQ-8]** Governance token design: governance-scoped, unit of account, or both?
- **[ODQ-9]** Minimum registration requirements: what floor prevents trivial project flooding?

### Resolved
- **[ODQ-4]** ✅ Source tree normalization: git initial commit tree hash re-hashed through BLAKE3; per-platform equivalents defined

---

## Provenance Declaration

This document was created on **May 27, 2026** by Mikeal Clark as the evolving concept record for OSSVI. Version 0.1.3 resolves ODQ-4 via git-anchored identity, establishes the two-tier owner/contributor model, introduces the Markov corpus-based Valuation Architecture, and decomposes PIN-2 into the shape problem (resolved) and the anchor problem (open). The OSSVI project itself will, upon launch, be registered as Project Hash ID #1 in its own registry.

---

*OSSVI Concept Document v0.1.3 — Private / Pre-Release — All rights reserved pending licensing decision*
