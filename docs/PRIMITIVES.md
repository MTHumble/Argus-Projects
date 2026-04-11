# Argus Infrastructure Primitives

> 12 infrastructure primitives derived from Claude Code source analysis.
> Audit conducted April 3, 2026. Score: 0.5/8 Day One, 0/4 Week One.
> Assessment: **design-complete, implementation-zero.**

## Audit Summary

Every primitive exists in architecture documents but none were enforced in running code at time of audit. The system was a blueprint without a building.

### Key Findings

1. **No permission enforcement** between LLM decision and tool execution
2. **No session persistence** — dropped sessions lose all context
3. **No token budget tracking** — runaway loop protection missing
4. **Silent failure is the dominant failure mode** — morning brief doesn't arrive and nobody knows until they notice the absence
5. **No system event logging** — troubleshooting is reactive "ask Claude about logs"

## The 12 Primitives

### Day One Primitives (8)

| # | Primitive | Status | Notes |
|---|---|---|---|
| 1 | **Trust Tier Enforcement** | IMPLEMENTED | Migration 039 live on CWF Supabase. 3 tables: `argus_tool_registry` (25 tools across 3 tiers), `argus_tool_executions` (audit trail), `argus_workflow_heartbeats` (dead man's switch). See [TRUST_TIERS.md](TRUST_TIERS.md). |
| 2 | **Dead Man's Switch** | DESIGNED | Heartbeat monitoring — detect when scheduled workflows fail silently. Table exists (`argus_workflow_heartbeats`), watchdog workflow spec written, not yet enforced. |
| 3 | **Session Persistence** | DESIGNED | Collapsed with Involuntary Encoding into one system. See [SESSION_PERSISTENCE.md](SESSION_PERSISTENCE.md). |
| 4 | **Token Budget Tracking** | DESIGNED | Max-iteration + wall-clock timeout on all n8n agent workflows. Not implemented. |
| 5 | **System Event Logging** | DESIGNED | Langfuse traces all model calls (architecture requirement). Not yet deployed. |
| 6 | **Tool Gate Enforcement** | IMPLEMENTED | PreToolUse hook at `~/.claude/hooks/tool-gate.js`. Reads from local cache of 78-tool registry. Tier 1 auto-allows, Tier 2 prompts, Tier 3 blocks. Audit log at `tool-gate.log`. SessionStart hook refreshes cache from CWF Supabase. |
| 7 | **Associative Retrieval** | DESIGNED | OB vector search before every response. Not automatic yet — manual `search_thoughts` calls only. |
| 8 | **Heartbeat System** | PARTIAL | 30-min lightweight pulse designed. Split from daily review. n8n Watchdog workflow published (5-min cron), detected stale heartbeat in Sprint 0 testing. |

### Week One Primitives (4)

| # | Primitive | Status | Notes |
|---|---|---|---|
| 9 | **Involuntary Encoding** | DESIGNED | Auto-distillation of conversations into thought fragments. Triggers: every 15 min, after mutations, on idle, on session close. Sprint 1 feature. |
| 10 | **Nightly Consolidation** | DESIGNED | 2am clustering, merging, linking, pruning job. Not implemented. |
| 11 | **Cognitive Integrity Protocol** | DESIGNED | Weekly recall test, monthly unscaffolded sprint, initiation latency tracking. See [COGNITIVE_INTEGRITY.md](COGNITIVE_INTEGRITY.md). |
| 12 | **Multi-User Interaction Model** | DESIGNED | Identify who Argus is talking to, adjust posture accordingly, onboarding interviews. Triggered by Sam login incident (March 31). |

## Top 3 Priorities (from audit)

1. **Trust tier enforcement table** — Weekend project. **NOW DONE.**
2. **Dead man's switch with heartbeat monitoring** — One evening.
3. **Max-iteration + wall-clock timeout on all n8n agent workflows** — One afternoon per workflow.

## Tool Registry Status

As of April 6, 2026:
- Registry covers **25 of ~103** MCP tools
- ~35 mutating tools are **ungoverned**
- ~63 read-only tools need batch-adding as Tier 1
- Registry expansion is a **Block 3 prerequisite**, not Block 2

## Governance Sprint Sequencing (April 6)

- Heartbeat status enum mismatch: table constraint enforces `'ok'/'error'/'timeout'`, spec incorrectly used `'healthy'`. Fix: update spec and all references to actual enum values.
- Proceed to Block 2 (Aspen Stand cockpit) — it reads from OB, not the registry
- Expand registry between Block 2 and Block 3
