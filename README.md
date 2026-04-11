# Argus

Autonomous Business Operating System for Applied Stoicism Labs.

Canonical home for Argus architecture specs, infrastructure primitives, failure patterns, and node configuration — reconstructed from Open Brain captures and Claude Desktop sessions.

## Documents

### Architecture & Design

| Document | Description |
|---|---|
| [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md) | Master blueprint — what Argus is, core stack, hybrid rationale, cognitive model |
| [docs/PRIMITIVES.md](docs/PRIMITIVES.md) | 12 infrastructure primitives with audit scores and implementation status |
| [docs/TRUST_TIERS.md](docs/TRUST_TIERS.md) | Three-tier permission enforcement system, tool registry, promotion rules |
| [docs/FAILURE_CATALOG.md](docs/FAILURE_CATALOG.md) | 8 named failure patterns with real incidents and detection rules |

### Protocols

| Document | Description |
|---|---|
| [docs/COGNITIVE_INTEGRITY.md](docs/COGNITIVE_INTEGRITY.md) | Anti-atrophy protocol — weekly recall test, monthly unscaffolded sprint, BRIEF-A |
| [docs/CONTRACT_ORCHESTRATION.md](docs/CONTRACT_ORCHESTRATION.md) | Constraint/schema spec pattern for autonomous action pipelines |
| [docs/SESSION_PERSISTENCE.md](docs/SESSION_PERSISTENCE.md) | Involuntary encoding, checkpoint spec, memory drift diagnosis |

### Infrastructure

| Document | Description |
|---|---|
| [node/NODE_SETUP.md](node/NODE_SETUP.md) | Compute node: RTX 5090, Ollama config, Docker stack, num_ctx fix |
| [specs/PHASE_ROADMAP.md](specs/PHASE_ROADMAP.md) | Phase 0/1/2 roadmap, sprint results, week 1-4 sequencing |

## Backfill Needed

Some documents contain `<!-- TODO -->` markers for content that was created in Claude Desktop sessions but not captured to Open Brain. To complete the knowledge base:

1. **CONTRACT_ORCHESTRATION.md** — Constraint file templates and schema file templates from April 10 Desktop session
2. **NODE_SETUP.md** — HTML build guide link
3. **FAILURE_CATALOG.md** — Push to public GitHub repo (MTHumble/failure-catalog)

## How to Use This Repo

This is a knowledge base, not a codebase. Documents are the product. Version control tracks how the architecture evolves over time — every decision change gets a commit.

Open Brain remains the source of truth for real-time state. This repo is the source of truth for *design decisions and specifications*.
