# Argus Trust Tier System

> Three-tier permission enforcement for all autonomous tool calls.
> Architecture decision locked April 3, 2026. Migration 039 deployed to CWF Supabase.

## Design Principle

This is a safety architecture, not a feature list. Every Argus action is classified by consequence severity. The system fails CLOSED — ambiguity means denial, not permission.

## Tier Definitions

### Tier 1 — Full Autonomy (12 tools)

Agent acts without approval.

| Tool | Description |
|---|---|
| Open Brain read | Search/list thoughts |
| Open Brain write | Capture thoughts |
| Slack internal posts | Post to internal ASL channels |
| MQTT printer reads | Monitor printer health, temperature, status |
| Brief generation | Morning brief, weekly synthesis |
| Heartbeat logging | System health check writes |
| Gap scan | Financial reconciliation |
| Research radar | Daily scans across 5 lanes |
| Data aggregation | Trend analysis |
| Routine status updates | Slack status messages |
| Memory capture | Open Brain organization |
| File reads | Local file system reads |

### Tier 2 — Draft and Approve (7 tools)

Agent prepares, Steve confirms via Slack reaction. **5-minute timeout = action DENIED.**

| Tool | Description |
|---|---|
| Manufacturing quotes | Agent calculates, Steve reviews before sending |
| Invoice generation | Agent prepares, Steve approves |
| External messages | Email/Slack to external parties |
| Xometry job bids | Bid preparation |
| Printer commands | Direct printer control |
| Schedule changes | Optimization recommendations |
| Sam task assignments | Task delegation |

### Tier 3 — Human Only (6 tools)

Agent CANNOT execute. Provides analysis only, Steve decides.

| Tool | Description |
|---|---|
| Stripe payments/refunds | No unsupervised financial ops |
| Contract acceptance | Legal commitments |
| Customer onboarding | New customer setup |
| Pricing changes | Strategy modifications |
| Data deletion | Permanent destructive operations |
| Regulatory interactions | Compliance-facing actions |

## Promotion Rule

A task moves from Tier 3 to 2, or Tier 2 to 1, ONLY after demonstrating **>95% success rate on 20+ consecutive executions** of that specific workflow. No exceptions.

## Enforcement Mechanism

### n8n Tool Gate

Every tool execution flows through an n8n Tool Gate function node that reads `argus_tool_registry` before execution:

1. Tool call requested
2. Tool Gate queries registry for tier classification
3. Tier 1: execute immediately
4. Tier 2: post draft to Slack, wait for reaction (5-min timeout → DENY)
5. Tier 3: block execution, log attempt

### Claude Code Hook

PreToolUse enforcement at `~/.claude/hooks/tool-gate.js`:
- Reads from local cache of 78-tool registry
- Tier 1 auto-allows
- Tier 2 prompts user
- Tier 3 blocks
- Audit log at `tool-gate.log`
- SessionStart hook refreshes cache from CWF Supabase

### Database Tables (Migration 039)

Deployed to CWF project `kmtagaecbqnvavdkmdwl`:

- **`argus_tool_registry`** — 25 tools across 3 tiers (needs expansion to ~103)
- **`argus_tool_executions`** — Permission audit trail for every tool call
- **`argus_workflow_heartbeats`** — Dead man's switch table

## Open Issues

- Registry covers 25 of ~103 MCP tools. ~35 mutating tools ungoverned.
- Heartbeat status enum mismatch: table enforces `'ok'/'error'/'timeout'`, spec had `'healthy'`
- Registry expansion is a Block 3 prerequisite
