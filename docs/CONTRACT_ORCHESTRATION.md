# Contract-Based Orchestration

> Constraint/schema spec pattern for autonomous action pipelines.
> Architecture decision locked April 10, 2026. First implementation: Trotline submit-application.

## The Pattern

Every autonomous action pipeline is governed by a **contract** — a set of constraints, schemas, tests, and failure conditions that define what the pipeline CAN and CANNOT do. The contract is the spec layer. The implementation serves the contract, not the other way around.

**Key principle:** `submit-application` is the first real Level 4 handoff — build it fully, then copy the pattern to every future action.

## Contract Components

### Constraints

Named, enforceable rules that gate pipeline progression:

| Constraint | Definition | Enforcement |
|---|---|---|
| **AUTH_REQUIRED** | Gates on `platform.tos_restricted` flag OR score below `auto_apply_threshold` (85). Tier 1 auto-apply, Tier 2 queue-for-approval. | Edge Function check |
| **NO_EMPTY_SUBMISSION** | Blocks SUBMITTED state without selected resume + cover letter variants. | DB trigger |
| **CONTEXT_BOUND** | Only approved `resume_blocks` used in generation. V2: LLM-judge or embedding check verifying generated prose introduces no claims absent from source blocks. | V1: block matching. V2: LLM-judge |
| **TARGET_MATCH** | Score >= 75 to approve a listing for application. | Edge Function check |
| **STATE_VALIDATION** | Blocks invalid state transitions (e.g., PENDING → RUNNING). Must be APPROVED first. | DB trigger |
| **TOOL_SANITY** | No external API calls in v1. | Deferred to v2 |

### "Emphasize Never Exaggerate"

The CONTEXT_BOUND constraint enforces a core principle: generated application materials may emphasize what's in the source resume blocks but must never introduce claims that aren't there. V1 achieves this through deterministic block matching (no AI generation). V2 adds LLM-judge verification.

### Schemas

Database tables that enforce the contract:

- **`trotline_tasks`** — State machine: PENDING → APPROVED → RUNNING → FAILED/COMPLETE. Fields: `attempt_count`, `max_retries`, `error_log`
- **`trotline_application_drafts`** — `resume_variants` + `cover_letter_variants` as JSONB, selection tracking, status (GENERATED/SELECTED/SUBMITTED)
- **`trotline_health`** — Pipeline health view: queued, approved, apps_this_week, stale_pending, failed_tasks

### Tests

| Test | Type | Status |
|---|---|---|
| STATE_VALIDATION trigger | SQL constraint | PASSED |
| NO_EMPTY_SUBMISSION trigger | SQL constraint | PASSED |
| Positive state transitions | SQL constraint | PASSED |
| Negative state transitions | SQL constraint | PASSED |
| 7 API smoke tests | curl | PASSED |
| Full integration test | SQL (with rollback) | PASSED |
| HITL walkthrough | Manual | PASSED |

### Failure Conditions

- State validation violations logged to `error_log`
- Failed tasks increment `attempt_count` up to `max_retries`
- **OB_CAPTURE fires on every state transition** with `TROTLINE STATE` prefix for audit trail

## Action Registry Pattern

Each action is one markdown file in `aspen-stand/actions/` — **the file IS the Claude Code build envelope.** This collapses specification and implementation into one artifact.

## Human Test Cycle (Hard Gate)

Any UI change MUST include:
1. API smoke tests run by Argus BEFORE handing to Steve
2. Numbered HITL walkthrough script for Steve to execute
3. Steve reports what he sees at each step
4. Argus verifies DB state against expected outcomes

No UI change ships without both API and HITL layers passing.

## Stability and Promotion

- Stability testing starts at **70%** for tuning
- Promotion to auto-apply gated at **95% over 20-run holdout** — matching the existing tier promotion rule from [TRUST_TIERS.md](TRUST_TIERS.md)

## Process Constraints

- **Git push is confirm-first** — Steve noted drift when Argus pushed without asking
- Edge Function endpoints are curl-testable — smoke test before every HITL handoff
- Five-slot handoff envelope enforced in AGENTS.md

## Implementation Status

| Component | Status |
|---|---|
| 4 Supabase migrations | APPLIED |
| 11 resume blocks approved | DONE (were all false — pipeline was structurally dead) |
| Edge Function written | DONE — NOT YET DEPLOYED (tool-gate Tier 3 blocked) |
| Frontend wired | DONE (approve buttons, draft review, health banner) |
| 4 constraint tests | PASSED |
| Full integration test | PASSED |

### Deploy Blocker

Edge Function needs manual deploy:
```bash
supabase functions deploy trotline-actions --project-ref ncdhdnvxtfutorcyoibu
```
Tool-gate Tier 3 blocked automated deploy.

<!-- TODO: Populate constraint file templates and schema file templates from Desktop session exports. The specific .md file formats for constraints and schemas were built in Claude Desktop on April 10 but not captured to OB. -->
