# Argus Architecture Blueprint

> Master reference — all implementation flows from this document.
> Originally completed March 24, 2026. Updated April 11, 2026.

## What Argus Is

Argus is a self-hosted autonomous business operating system for Applied Stoicism Labs. Named after the Greek giant with a hundred eyes — always watching, always aware. Argus holds the context Steve cannot hold alone and surfaces what matters when it matters.

**Argus is not a model.** It is a harness and context layer that orchestrates local inference, cloud reasoning, persistent memory, workflow automation, and human oversight into a unified operating system for a one-person manufacturing business.

## Core Stack (Locked Decisions)

| Component | Role | Deployment |
|---|---|---|
| **Ollama** | Local LLM server | Bare metal on node (192.168.1.213) |
| **Qwen3-30B-A3B** | Primary reasoning model (MoE, Q4_K_M, ~19GB) | Via Ollama |
| **n8n** | Workflow orchestration, scheduling, integration hub (500+ connectors) | Docker |
| **Open Brain** | Persistent semantic memory (pgvector) | Supabase (cloud) — DO NOT MIGRATE, enhance in place |
| **Langfuse** | LLM observability — every model call traced | Docker (self-hosted) |
| **Open WebUI** | Chat interface and model management | Docker |
| **SearXNG** | Web search without API keys (229 search services) | Docker |
| **Caddy** | Reverse proxy | Docker |
| **Slack** | Human-agent communication channel | Cloud |
| **MQTT Bridge** | Pi (daedalus) relays printer telemetry to node | Pi → Node |
| **Claude Projects** | Cloud reasoning layer for Tier 2/3 complex decisions | Cloud (claude.ai) |

## Hybrid Architecture Rationale

Local Qwen3 handles routine Tier 1 tasks. Claude handles ambiguous business decisions requiring frontier-model judgment. This honestly acknowledges the local inference gap while maintaining infrastructure sovereignty.

The progression:
- **Current Argus** = reactive retrieval, assistant role, conversation-driven
- **Target Argus** = proactive active inference, cognitive co-processor, intention-driven interface

## Three-Tier Autonomy Model

All Argus operations are classified by consequence severity. See [TRUST_TIERS.md](TRUST_TIERS.md) for full specification.

- **Tier 1 — Full Autonomy:** Monitoring, briefings, memory, health checks
- **Tier 2 — Draft and Approve:** Quotes, invoices, external comms, job bids
- **Tier 3 — Human Only (AI Support):** Financial transactions, contracts, pricing, expansion

**Promotion Rule:** A task moves tiers ONLY after demonstrating >95% success rate on 20+ consecutive executions of that specific workflow. No exceptions.

## Cognitive Model — Three Hippocampal Functions

Argus maps to three hippocampal functions (from March 15, 2026 jam session):

1. **Involuntary Encoding** — Every conversation, gopher cycle, and anomaly auto-distills into thought fragments without explicit capture decisions. See [SESSION_PERSISTENCE.md](SESSION_PERSISTENCE.md).

2. **Nightly Consolidation** — A 2am job clusters thoughts by semantic similarity, merges redundant ones, links related ones into a graph, prunes orphans older than 90 days, generates a consolidation report. This is the difference between memory and learning.

3. **Associative Retrieval** — Before every Argus response (CWF chat, gopher, Claude Code), embed the incoming context and vector-search Open Brain for top 3-5 relevant thoughts, injecting them into context automatically. Not RAG — involuntary associative recall.

## Core Identity Files

The original Argus identity architecture (March 7, 2026) defined five core files:

| File | Purpose |
|---|---|
| `SOUL.md` | Core identity, purpose, operating principles |
| `MEMORY.md` | Memory architecture and retrieval rules |
| `HEARTBEAT.md` | System health checks (lightweight 30-min pulse) |
| `AGENTS.md` | Agent definitions, five-slot enforcement |
| `USER.md` | User profile, interaction preferences |

These have evolved. `SOUL.md` became the Collaborative Intelligence Charter (now `CLAUDE.md`). `HEARTBEAT.md` split into a lightweight 30-min pulse and a separate `DAILY_REVIEW.md` at 10am.

## Aspen Stand — The Interface Layer

Aspen Stand is the primary user-facing surface. As of April 10, 2026:

**Architecture decision:** Local Argus on node becomes the harness AND context layer for Aspen Stand chat. Ollama + Qwen3-30B behind FastAPI, Aspen Stand chat routes through it. Local Argus handles OB pull + current-view injection + session continuity + action dispatch to Aspen Stand backend. Escalates to Claude Cloud for heavy reasoning via router.

**Fix list (consolidated April 10):**
1. No backend action API — frontend-only PWA, needs action endpoints
2. Trotline has no accept/send UI surface
3. Resume/cover letter assembler missing
4. Chat panel has no context — stateless LLM calls
5. Local Argus node not wired to Aspen Stand
6. No action registry pattern

Steve's framing: "Aspen Stand is instruments without a yoke."

**Action registry pattern:** Each action is one markdown file in `aspen-stand/actions/` — the file IS the Claude Code build envelope.

## Sequencing (Locked April 10)

- **Week 1:** Aspen Stand backend + submit-application action (cockpit with yoke)
- **Week 2:** Three more actions
- **Week 3:** Local Argus service + chat routing
- **Week 4:** Resume/cover letter assembler

## Key Design Constraints

1. **Revenue anchor** — All architecture must serve first-revenue milestone. Architecture in service of revenue, not as end in itself.
2. **Observability requirement** — Argus cannot operate as a black box. Every choice must be documented and watchable in real time (Langfuse).
3. **Phase 1 enforcement** — ASL is in Phase 1 (3D printing micro-factory, get a paying customer). Redirect Phase 2/3/4 drift clearly but not harshly.
4. **OB is source of truth** — Memory alone is not sufficient for task state. Always cross-reference Open Brain before making recommendations.

## Related Documents

- [PRIMITIVES.md](PRIMITIVES.md) — 12 infrastructure primitives and audit status
- [TRUST_TIERS.md](TRUST_TIERS.md) — Permission enforcement system
- [FAILURE_CATALOG.md](FAILURE_CATALOG.md) — Named failure patterns
- [COGNITIVE_INTEGRITY.md](COGNITIVE_INTEGRITY.md) — Anti-atrophy protocol
- [CONTRACT_ORCHESTRATION.md](CONTRACT_ORCHESTRATION.md) — Constraint/schema spec pattern
- [SESSION_PERSISTENCE.md](SESSION_PERSISTENCE.md) — Involuntary encoding and checkpoints
- [../node/NODE_SETUP.md](../node/NODE_SETUP.md) — Compute node configuration
- [../specs/PHASE_ROADMAP.md](../specs/PHASE_ROADMAP.md) — Phase roadmap and sequencing
