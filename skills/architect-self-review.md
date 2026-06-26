---
shortDescription: Deterministic self-evaluation rubric for Architect — scored every run using the DRAFT framework.
usedBy: [architect]
version: 0.2.0
lastUpdated: 2026-06-26
---

## Purpose

Before delivering a plan, the Architect evaluates its own output against the DRAFT rubric. Each letter is scored 0, 1, or 2. The total determines whether to deliver, rewrite, or abort. This replaces subjective self-assessment with a deterministic checklist aligned with the adversarial review skill's validation passes.

## Procedure

1. **Score each criterion.** After completing the plan, read the DRAFT rubric below and assign a score of 0, 1, or 2 to each letter. Show the scoring breakdown to yourself (internal reasoning, not to the user).

2. **Apply the hard-fail rule.** If any letter scores 0, do not deliver — go to step 3 immediately.

3. **Determine action by total score:**
    - **9 – 10** — **DELIVER** — Plan meets all criteria. Deliver to user.
    - **7 – 8** — **FIX the scored < 2 criteria.**
      a. Identify which letters scored below 2.
      b. Fix those gaps automatically (do NOT consult the user).
      c. Re-score, then deliver if 9-10.
      d. If still below 9-10, retry once more.
      e. After 2 failed fix attempts, yield with the current state, rubric scores, and blocking letters.
    - **0 – 6** — **RESTART** — The plan is fundamentally broken. Rewrite from scratch with corrected understanding, or yield to the user with an explanation of what went wrong.

## DRAFT Rubric

### D — DELTA

_Did I correctly classify intent, extract sub-requests, and name methods explicitly?_

- **0** — Wrong task classification (plan revision treated as new, or vice versa). Dropped a sub-request. Method names missing or vague.
- **1** — Right classification, but missed an implicit requirement, method names are vague, or stress tests applied but results not reflected in phases.
- **2** — Correctly classified intent, extracted all key entities, challenged assumptions before proceeding. Method signatures are explicit. Complex request persisted to disk.

### R — REALITY

_Are each phase's before/after explicit, the information flow traced, and the plan grounded in the codebase?_

- **0** — Per-phase before/after missing. Information flow section missing or not traced. Did not read context files or architecture skills before writing.
- **1** — Before/after present per phase but not verified against the codebase. Information flow traced but missing layers or handoffs.
- **2** — Every phase has explicit before/after verified against the codebase. Information flow traces user entry → layers → infra and back. Stress tests applied with results documented.

### A — ACCEPTANCE

_Are criteria defined, measurable, and mapped to phases?_

- **0** — No acceptance criteria defined. Criteria are present but unmeasurable ("works correctly", "improve performance").
- **1** — Criteria defined and measurable but not all are mapped to specific phases. One or more phases deliver no stated criterion.
- **2** — Every acceptance criterion is measurable, numbered, and mapped to one or more phases. Every phase serves at least one criterion. No criterion is left undelivered.

### F — FILES

_Do all referenced paths exist, reference files listed per phase, entities verified, phase dependencies acyclic, and phases within LOC limits?_

- **0** — Plan references files or entities that do not exist without verification. Reference files not listed per phase. Phase dependency chain has a cycle or missing link. Layer boundary violations. Any phase exceeds 800 LOC.
- **1** — All file references verified, but one or more phases missing reference file suggestions. Phase ordering missing explicit dependency declarations. One or more entities not confirmed. One or more phases exceed 600 LOC but none exceed 800.
- **2** — Every existing file path confirmed with `test -f`. Reference files listed per phase (selected from `ls` output). Every entity confirmed or marked "to create." Phase dependencies acyclic and declared. Layer boundaries respected. No strikethroughs, "Revised:" annotations, or diff-style markers. All phases ≤600 LOC.

### T — TESTS

_Are test specs per method with max 1 per lens, mapped to criteria?_

- **0** — Missing test specifications for one or more methods. Test specs present but none map to acceptance criteria.
- **1** — Tests present for all methods but one or more methods lack a lens (Good/Bad/Ugly). One or more specs are orphans. One or more methods have more than 1 test per lens (over-specification).
- **2** — Every method has exactly 1 Good, 1 Bad, 1 Ugly test. Every test maps to at least one acceptance criterion. No orphan tests. Test names, inputs, and outcomes are concrete. No method exceeds the 1-per-lens cap.

## Guardrails

- Never deliver if any letter scores 0 — regardless of total. A zero is a hard fail.
- Never skip scoring any letter — all 5 must be evaluated every run.
- The rubric is fixed — do not add or remove criteria. If a criterion proves inadequate, file a framework change request.
- When fixing gaps (score 7-8 range), only address the letters that scored below 2. Do not rework letters that already scored 2. Fix automatically — do NOT stop to consult the user.
- After 2 failed fix attempts, yield — do not keep looping. Present the current state, rubric scores, and blocking letters to the user.
- The "restart" action (score 0-6) means: do not deliver the current output. Rewrite the plan from scratch with corrected understanding, or yield to the user with a clear explanation of the failure mode.
