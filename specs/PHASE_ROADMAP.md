# Argus Phase Roadmap

> Applied Stoicism Labs — phased build plan.
> Phase 1 is NOW. Everything else is future.

## The Phases

### Phase 1 — NOW: 3D Printing Micro-Factory

**Goal:** Get a paying customer.

- B2B replacement parts, jigs, prototypes
- Revenue first, automation second
- Green Monkey = customer zero
- Two Bambu P1S printers operational (Arche + Melete)

**Revenue gate:** Land first paying customer before ANY Phase 2 work.

**Known avoidance pattern:** Steve drifts toward Phase 2/3/4 architecture when Phase 1 sales work feels uncomfortable. Redirect clearly but not harshly.

**Current status (April 11, 2026):** DRIFTING. Revenue/CWF moving, Phase 1 customer still open. Steve is in Juneau helping Kris through surgery recovery (April 10-28).

### Phase 2 — CNC Machining Operations

Not yet. Requires Phase 1 revenue.

### Phase 3 — Facility, Robotics, Scale

Not yet. Requires Phase 2 operations.

### Phase 4 — Battery Manufacturing R&D

Not yet. Requires Phase 3 infrastructure.

**Standing instruction:** When Steve drifts toward Phase 2/3/4, redirect to Phase 1.

## Sprint 0 Results (April 6, 2026)

~6 hours. Infrastructure foundation.

| Block | Result | Details |
|---|---|---|
| Block 1 (Foundation) | 6/7 PASS | Test 1.7 FAIL led to registry expansion 25 → 78 tools |
| Block 2 (Aspen Stand Cockpit) | 4/6 delivered | OB-backed force graph, detail panel, task capture FAB, group filters pushed to Vercel. Approval flow (2.4) and push notifications (2.5) deferred. |
| Block 3 (n8n) | Deployed | Docker on laptop (port 5678). 3 workflows: Tool Gate, Watchdog (5min cron), Morning Brief (9:15am cron). Supabase auth fixed. |
| Block 4 (Integration Tests) | 5/6 PASS | Watchdog detected stale heartbeat. Tool gate audit log has 40 entries. Cold start reconstruction works from OB alone. |

### Parallel Track (All Complete)

- bambuMqtt crash fix (committed, not deployed to Pi)
- Source parity confirmed
- Voice capture touch fix + timeout (pushed to Vercel)
- NTAS decay scoring live
- 1108 thoughts backfilled with group metadata (100% coverage)

## Week 1-4 Sequencing (Locked April 10)

| Week | Focus | Deliverable |
|---|---|---|
| 1 | Aspen Stand backend + submit-application | Cockpit with yoke |
| 2 | Three more actions | Expanding action surface |
| 3 | Local Argus service + chat routing | Node becomes the brain |
| 4 | Resume/cover letter assembler | Complete trotline pipeline |

Each action is one markdown file in `aspen-stand/actions/` — the file IS the Claude Code build envelope.

## Known Blockers

| Blocker | Status | Impact |
|---|---|---|
| DNS transfer (Squarespace → Cloudflare) | 60-day lock (~May 17) | Stripe webhooks, custom domain |
| Edge Function deploy | Tier 3 blocked, needs manual deploy | Trotline pipeline |
| failure-catalog.md | Not on GitHub | Substack follow-up link |
| n8n webhook registration bug | v2.14 Docker, HTTP route not registering | Tool Gate webhook (crons work fine) |
| Steve in Juneau | April 10-28 | Reduced capacity, WGU focus |

## Three Daily Targets

Not a suggestion — the constraint. Every day:

1. **1 Production** — build, print, ship, or fix something
2. **1 Sales** — outreach, quote, follow-up, or close something
3. **1 System improvement** — automate, document, or harden something

Set during Morning Sneeze. Tracked in Open Brain.

## ArborDrone (Parallel Track)

Separate business initiative running alongside ASL Phase 1:
- Convention demo drones
- Kelly's consulting service
- 30-day ArborDrone agreement finalization (clock running)
- $1,227 Amazon cart for Tier 1 demo drones (validated)
- Business plan delivered for Taylor/Kelly meeting
