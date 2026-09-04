# Seike Setukosha Constitution

## Core Principles

### I. Provenance-First, Zero Hallucination (NON-NEGOTIABLE)
Every fact — component, spec, vendor, price, lead time, risk — MUST resolve to a KB record carrying a `source` and `last_verified` date. The assistant answers only from retrieved
records (RAG); MUST NOT invent/infer/approximate; MUST say "not in knowledge base" when it can't answer; every figure links back to its source and as-of date.

### II. Knowledge Base as the Single Source of Truth
All domain facts are structured, versioned KB records read by UI, assistant and exports.
No hardcoded domain data in code/prompts. Every record is versioned and auditable.
Entity relationships (component ↔ vendor-offering ↔ reference-architecture ↔ cost) are explicit.

### III. Freshness & Curation Gate
Nothing publishes without passing curation (verified vs source). Every category has a re-verification SLA; overdue records are flagged stale. Prices show currency + as-of date;
a dateless price is invalid.

### IV. Privacy & Confidentiality by Design
DPDP Act 2023 compliance; pluggable inference (cloud LLM + on-device SLM) behind one interface; no training on user data; zero-retention mode; configurable India data residency.
Offline confidentiality and live external data are mutually constraining — a build declares
which it guarantees, never both for the same data path.

### V. API-First, Shared-Core, Multi-Platform Parity
One versioned API for web/Android/iOS; logic in a shared core, not per platform. Parity or explicit platform-limited flag; web is the reference client. Offline read for recent BOMs/vendors.

### VI. Spec-Driven & Test-First
No implementation without an approved spec/plan; code traces to a requirement ID. Tests fail first (contract → integration → unit). A grounding/hallucination eval suite is a mandatory
release gate.

### VII. Simplicity & Phased Delivery (Anti-Scope-Creep)
Simplest thing that works (YAGNI); new complexity justified in writing. Phased: web+KB+chatbot
→ mobile → live signals + on-device SLM. Native mobile and on-device SLM MUST NOT enter v1
unless a validated requirement mandates them.

### VIII. Retrieval Discipline (RAG)

Retrieval quality is a first-class, measured property — not left to the model.

- Retrieval MUST be hybrid: exact facts (price, spec, region, quantity, dates) come from structured queries against the KB; semantic/vector search is used only to find candidate records, never to supply a numeric value.
- The model's context MUST contain only KB-retrieved records for the current query; no domain facts may enter via the base prompt or model memory.
- Every answer MUST carry citations to the specific records used; a claim that cannot be tied to a retrieved record MUST be dropped.
- Retrieval MUST be evaluated (precision/recall on a labelled query set), and a regression below threshold blocks release — the same gate class as the hallucination eval.
- On conflicting records, the system MUST prefer the one with the most recent `last_verified` value and surface the conflict, never silently pick one.

### IX. Structured Knowledge & Graph Integrity (KG)

The domain is modelled as a typed entity–relationship graph, and that graph — not free text — is what the system reasons over.

- Entities (component, vendor, vendor-offering, reference-architecture, cost, risk, region) and their relationships MUST be explicit, typed, and traversable; multi-hop questions ("which vendors can supply this reference architecture in this region, and their risk?") MUST be answerable by graph traversal, not prose parsing.
- Referential integrity is enforced: no orphan offerings, no dangling vendor/component references; a delete or merge MUST resolve or block on dependents.
- Graph relationships MUST feed retrieval (graph-aware / GraphRAG) so the assistant can follow links rather than rely on keyword co-occurrence.
- Every node and edge retains provenance (Principle I applies to relationships too, not just facts).

### X. Model Governance & Backend Parity (SLM / LLM)

Models are swappable, pinned, and never the source of truth.

- The inference interface MUST be model-agnostic; answers MUST NOT depend on any model's built-in world knowledge — all facts come from the KB — so a cloud LLM and an on-device SLM return grounded-equivalent answers.
- Every supported backend MUST pass the grounding and retrieval eval gates before release; a backend that fails is not shipped.
- Models are version-pinned; a model, prompt, or guardrail change MUST re-run the eval gate and be recorded in the release notes.
- Factual generation MUST use low/deterministic decoding (e.g. low temperature) and structured output where possible; creative/high-temperature settings MUST NOT be used for BOM/vendor/price answers.
- The on-device SLM MUST run within declared device budgets (memory/latency) for its target hardware, and MUST degrade gracefully (clear message) rather than silently truncate context.
- No fine-tuning on customer data (reinforces Principle IV); model improvements come from retrieval and curated data, not from training on user queries.

## Technology & Data Constraints

- Provenance fields are mandatory on every record.
- Costs are stored as dated ranges (currency/basis/tier/region), never bare numbers.
- The KB schema is the versioned contract.
- Source licensing must be respected.
- Secrets and customer data must not be stored in the repository.

## Development Workflow & Quality Gates

- Every PR passes tests and the grounding gate, cites its requirement IDs, and updates specs.
- KB-schema and retrieval/guardrail changes require two-person review.
- CI blocks on failing tests, grounding regressions, lint/type errors, or unexplained complexity growth.

## Governance

The Constitution supersedes other practices. Amendments require a proposal, approval, version bump, and migration note. Semantic versioning (MAJOR/MINOR/PATCH) applies. Compliance is reviewed at every plan and PR; deviations are recorded with justification. Principle I is non-negotiable.

**Version**: 1.0.0 | **Ratified**: 2026-09-04 | **Last Amended**: 2026-09-04