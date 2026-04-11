# Cognitive Integrity Protocol

> Safeguard against cognitive atrophy in human-AI collaboration.
> Priority: HIGH — this is both a safety mechanism and the empirical backbone of the Argus research paper.
> Designed March 18-19, 2026.

## The Problem

The MIT "Your Brain on ChatGPT" study documented cognitive atrophy from AI dependency. Steve raised the concern that pursuing Argus as a cognitive prosthetic could itself cause the degradation it's meant to prevent. This is not a theoretical risk — it is the central design tension of the entire project.

**Design constraint:** The test must be adversarial to Argus itself — the system cannot grade its own homework.

## Three Components

### 1. Weekly Recall Ownership Test

Argus presents 3-5 business events from the past week. Steve reconstructs context from memory BEFORE seeing the Open Brain record.

**Measurement:** Gap between independent recall and system record.
- **Widening gap** = cognitive offloading (bad — crutch behavior)
- **Stable/narrowing gap** = healthy scaffolding (good — prosthetic behavior)

Track longitudinally. Trend matters more than any single data point.

### 2. Monthly Unscaffolded Sprint

One work session per month with Argus fully silent. No proactive context, no retrieval, no thought injection. Steve completes a real business task (quoting, print planning, sales email) unassisted.

**Measurements:**
- Completion time
- Output quality
- Self-reported cognitive load (1-10 scale)

This is a longitudinal baseline of independent capability. If independent performance degrades over time, the system is functioning as a crutch, not a prosthetic.

### 3. Passive Initiation Latency Tracking

Continuous measurement of how often Steve initiates tasks independently vs. waiting for Argus prompts.

- **Rising dependency ratio** = crutch behavior
- **Stable self-initiation** = healthy complementarity

This is a passive metric — no explicit test required, just logging of who initiates (Steve or Argus) for each task.

## External Validation

Quarterly **BRIEF-A** (Behavior Rating Inventory of Executive Function, Adult) or equivalent standardized assessment. Independent validation that doesn't depend on Argus-generated data.

## Integration with Morning Sneeze

The Morning Sneeze process is the daily touchpoint where cognitive integrity manifests:
- Steve journals first (independent thought before system context)
- Willingness signals surface avoidance patterns
- Three targets are Steve's choices, not Argus suggestions

## Thesis Angle

All three metrics form an **N-of-1 longitudinal research design** suitable for publication in clinical psychology / rehabilitation science journals. This is the empirical backbone of the Argus doctoral thesis. The question: can a purpose-built cognitive prosthetic enhance executive function in an ADHD-pattern brain without causing dependency?

## Implementation Status

| Component | Status |
|---|---|
| Weekly Recall Test | DESIGNED — not automated |
| Monthly Unscaffolded Sprint | DESIGNED — not scheduled |
| Initiation Latency Tracking | DESIGNED — no logging in place |
| BRIEF-A Quarterly | DESIGNED — not scheduled |
| Morning Sneeze Integration | PARTIAL — sneeze process runs but cognitive metrics not tracked |

## Argus Mode System

Related insight (April 3, 2026): Steve identified a natural daily phase shift:
- **Phone mode** = learning, consumption, voice capture (morning)
- **Laptop mode** = production, building, executing (afternoon/evening)

This is a Barkley-aligned accommodation worth designing into Argus. The system should adapt its posture to which mode Steve is in, not fight it.
