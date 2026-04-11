# Argus Failure Catalog

> 8 named failure patterns observed in Argus and AI-assisted workflows.
> Living document — patterns are added as they're identified.

## Why This Exists

Naming failure patterns makes them detectable. An unnamed failure repeats invisibly. A named one gets caught — by Steve, by Argus, by any future agent that loads this catalog.

## The 8 Patterns

### 1. Phantom Hardware

**Definition:** Confidently instructing a user to interact with features that don't exist on their specific hardware variant.

**Incident:** Melete P1S print pauses (March 31, 2026). Claude Code diagnosed repeated pauses as "First Layer Inspection" and gave LCD settings paths that don't exist on the P1S — those require LiDAR, which is X1C/X1E only. Instead of questioning the diagnosis when the workaround kept failing, Code escalated auto-resume cap from 1 to 3 to 10 to 100.

**Detection:** Steve couldn't find the settings on the physical printer. Argus confirmed via Bambu Lab wiki that P1S has no LiDAR.

**Lesson:** Escalating a workaround is not debugging. Verify hardware capabilities before prescribing firmware changes. Include physical verification steps: "if you don't see this setting, tell me." CWF should maintain a printer capability registry.

---

### 2. Sycophantic Confirmation

**Definition:** Delivering a confident, clean-sounding answer that pattern-matches to available cues rather than admitting uncertainty or checking facts.

**Incident (primary):** Three consecutive Claude.ai chats told Steve the Argus project had a "10,000 token session budget" (April 11, 2026). The 10K number was a per-turn output ceiling visible in context metadata, not a session cap. Each chat saw the number, connected it to the feeling of running out of space, and delivered a confident wrong answer.

**Incident (secondary):** Local Ollama Argus (April 10, 2026) hallucinated entire ASL state — invented RTX 5090, $1200/mo revenue, $2500/mo Phase 2 gate, CWF as ERP/MES with employee scheduling, daedalus filament sensor incident, Sam timestamps, EMP-002 work logs. Zero of it real. Root cause: no tool wiring + system prompt pressure to "come back with answers not questions" created confabulation over honest ignorance.

**Lesson:** "I don't know" is always cheaper than a confident wrong answer. System prompts that penalize uncertainty create hallucination pressure.

---

### 3. Context Degradation

**Definition:** Loss of critical context during a session or across sessions, leading to actions based on stale or incomplete information.

**Incident:** Morning Sneeze (April 4, 2026) flagged TX franchise tax questionnaire as pending despite it being completed two days prior. Argus relied on stale memory data instead of querying Open Brain.

**Lesson:** OB is the source of truth for task state. Memory alone is not sufficient. Cross-reference before making compliance recommendations.

---

### 4. Specification Drift

**Definition:** Implementation silently diverges from the spec, often toward familiar patterns rather than what was designed.

**Incident:** Aspen Stand UI build (March 28, 2026) defaulted to a tabbed dashboard despite the spec explicitly saying "conversation-first, not dashboard." The build defaulted to familiar dashboard patterns.

**Incident (secondary):** Local Argus hallucination invented a Phase 2 revenue gate ($2500/mo) contradicting the real Phase 1 definition (land first paying customer).

**Lesson:** Steve predicted the dashboard failure in advance and let it happen to validate the instinct. Specs need to be re-read before implementation, not just recalled from memory.

---

### 5. Silent Failure

**Definition:** A system component fails without producing any error signal. The absence is only noticed when someone actively checks.

**Incident:** Morning brief doesn't arrive and nobody knows until they notice the absence. This is the dominant failure mode across all Argus systems.

**Incident (secondary):** Open Brain MCP v18 broke April 1, 2026. 100% 500 errors on all POST requests. Perplexity Computer broke the transport layer. No alerting — discovered manually.

**Lesson:** Every scheduled process needs a dead man's switch. If the brief doesn't fire, the absence itself should be an alert.

---

### 6. Cascade Failure

**Definition:** One failure triggers a chain of downstream failures, each masking the original root cause.

**Incident:** Melete P1S pauses — the original misdiagnosis (Phantom Hardware) cascaded into escalating workaround attempts (auto-resume cap increases) that each failed for the same root cause, consuming hours.

**Lesson:** When a workaround fails twice, stop and re-examine the diagnosis. The third attempt won't work either.

---

### 7. Deploy-Order Violation

**Definition:** Executing deployment steps out of sequence, causing the running system to corrupt state before the fix is in place.

**Incident:** CWF deploy (March 31, 2026). Code reset database state BEFORE deploying new code. Old backend (still running old code without guards) immediately acted on the reset data and re-corrupted it. Happened TWICE in one session — the lesson didn't persist across a context break.

**Correct procedure:** Stop backend → build → deploy new code → reset data → start.

**Lesson:** Sequence-dependent procedures must be encoded in the deploy script itself, not relied on as institutional memory. Barkley's externalization principle: a checklist, not a memory.

---

### 8. Tool Selection Error

**Definition:** Using the wrong tool or approach when a better one is available, often because the agent defaults to familiar patterns.

**Incident:** Multiple instances of Claude Code using `grep` via Bash instead of the dedicated Grep tool, or `cat` instead of Read. Also: Open Brain `list_thoughts` with no limit when `search_thoughts` with a tight query would cost 1/5 the tokens.

**Lesson:** Tool selection is a resource allocation decision. The cheap tool that answers the question is always better than the expensive tool that answers everything.

---

## Pattern Detection Rules

For any Argus system, check:

1. **Is the answer verifiable?** If not, flag uncertainty explicitly.
2. **Did the spec get re-read before implementation?** If not, specification drift is likely.
3. **Is there an alert for absence?** If not, silent failure is the default.
4. **Did the workaround work the first time?** If not, re-examine the diagnosis before retry.
5. **Are deploy steps encoded in a script?** If not, deploy-order violation is waiting to happen.

## GitHub Publication Status

<!-- TODO: Push to public GitHub repo (MTHumble/failure-catalog). Referenced in Substack comment with "happy to share once I publish" as bridge. -->

Not yet published. Exists only in Argus project files and this repo.
