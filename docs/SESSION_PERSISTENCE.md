# Session Persistence & Involuntary Encoding

> How Argus maintains state across sessions and auto-captures knowledge.
> Decision locked April 6, 2026. Collapses Primitive #3 and Involuntary Encoding into one system.

## The Problem

Dropped sessions lose all context. The current system relies on manual Open Brain captures and conversation memory, both of which are lossy. The gap between what Steve and Argus discuss and what persists is where "memory drift" lives.

### Memory Drift Diagnosis (April 11, 2026)

The "10,000 token session" misdiagnosis revealed the real problem: **pre-load bloat**, not a hard context cap. Claude.ai projects load custom instructions + knowledge files + userMemories + tool schemas for every connected MCP on every turn. When this pre-load is heavy, the usable context for actual work shrinks dramatically.

**Contributing factors:**
- `list_thoughts` with no limit dumps full capture bodies inline (5-9K tokens per pull)
- Stale memories (wrong GPU, old sales leads) load every chat
- Every enabled MCP connector loads tool schemas even when unused
- Project knowledge files duplicate what's in Open Brain

**Fix levers (in order of impact):**
1. Connector triage — disable project-default MCPs not used every session
2. OB query discipline — `search_thoughts` with tight queries, not `list_thoughts`
3. userMemories pruning — move historical detail to OB, keep only load-bearing facts
4. Project knowledge audit — if OB has it, delete from project knowledge

## Two-Mode System

### Mode 1: Structured Checkpoint (Sprint 0 — always on)

Lightweight, no LLM required. Fixed schema.

**Triggers:**
- Every 15 minutes during active session
- After mutations (file writes, DB changes, deploys)
- On idle detection
- On session close

**Captured data:**
- Timestamp
- Active files/contexts
- Recent actions taken
- Open decisions pending
- State of in-progress work

### Mode 2: Intelligent Distillation (Sprint 1 — when LLM available)

Deeper extraction layer on top of checkpoints.

**Output:** 1-3 thought fragments per interval, classified as:
- `observation` — what was noticed
- `decision` — what was decided
- `task` — what needs doing
- `idea` — speculative/generative

Each fragment is linked to existing Open Brain thoughts when relevant.

**Execution:**
- Primary: Local Qwen3 on Argus node (when live)
- Fallback: Claude API (stopgap until node is operational)

## Hippocampal Model

This system implements the first of three hippocampal functions:

1. **Involuntary Encoding** (this document) — auto-capture without explicit decisions
2. **Nightly Consolidation** — 2am clustering, merging, linking, pruning (not yet implemented)
3. **Associative Retrieval** — vector search before every response (manual only, not automatic)

## Hall Monitor Pattern

The auto-capture timeout uses a "hall monitor" pattern (from Agent Queue Architecture, March 24):

- **v1 (current):** Claude Code `/loop 30m` — check last OB capture timestamp, fire auto-capture if stale during active hours (8am-11pm CDT). Session-scoped, dies when terminal closes.
- **v2 (node online):** `aspen_monitor.sh` as cron job on Argus node — zero tokens, never sleeps, survives reboots.

Pattern: cheap outer loop watches the clock, intelligence only invoked when work exists.

## Constraints

- v1 `/loop` is session-scoped (dies on terminal close or laptop sleep)
- 3-day auto-expiration (must restart periodically)
- Burns API tokens per cycle (~16-32 calls/day at 30min intervals)
- Only monitors capture velocity, not business operations

## Implementation Status

| Component | Status |
|---|---|
| Structured checkpoint schema | DESIGNED |
| Checkpoint triggers | DESIGNED |
| LLM distillation | DESIGNED (Sprint 1) |
| Hall monitor v1 (`/loop`) | DESIGNED — spec in ASPEN_STAND_V1_SPEC.md |
| Hall monitor v2 (cron) | BLOCKED on Argus node |
| Nightly consolidation | DESIGNED |
| Associative retrieval | MANUAL only |
