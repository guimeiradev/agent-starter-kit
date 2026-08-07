---
shortDescription: Iterative alignment interview that resolves compounding ambiguity before planning begins, building a shared domain brief.
usedBy: [maestro, architect]
version: 0.1.0
lastUpdated: 2026-08-06
---

## Purpose

`skills/agent-decision.md` resolves one ambiguous decision at a time with a single 1-3-1 escalation. A new feature or project request often carries several unresolved branches at once — an escalation on the loudest one leaves the rest to be guessed. This skill runs a bounded, multi-round interview that surfaces every unresolved branch, resolves them in batches, and persists the answers as a domain brief the Architect plans from directly instead of re-deriving intent from a single conversational turn.

## Procedure

1. **Trigger conditions.** Run this skill when a new feature or project request has more than one unresolved branch — multiple approach choices, ambiguous requirements, or missing domain definitions. Do not run it for bug fixes (route those to `skills/bug-diagnosis.md`) or for requests with a single isolated gap (escalate directly via `skills/agent-decision.md` instead).

2. **Create the brief file.** `mkdir -p .memory/brief && ` create `.memory/brief/YYYY-MM-DD-<slug>.md` using the schema below before the first round starts.

3. **Round 1 — surface every branch.** Classify each unresolved point using `skills/agent-decision.md`'s five types. Batch every "ambiguous requirement" and "missing information" branch into one round — up to 5 questions — formatted as one 1-3-1 block per branch (problem, 3 options with trade-offs, 1 recommendation). "Approach choice" and "minor ambiguity" branches do not need a question — record the documented default directly into the brief.

4. **Record answers as they arrive.** Append each resolved branch to the `## Resolved` section of the brief immediately — do not hold answers in memory across rounds.

5. **Repeat or stop.** After each round, re-scan: does a new answer reveal a branch that wasn't visible before? If yes, run another round (max 5 new questions). Stop when a round surfaces zero new branches, or after 3 rounds total, whichever comes first.

6. **Close out.** If branches remain open after 3 rounds, list them under `## Open` in the brief — the Architect will yield on these per its own step 11. Otherwise the brief has no `## Open` section.

7. **Hand off.** Deliver the brief file path. It gets attached to every downstream dispatch for this request (uses: `skills/dispatch.md`).

## Schema

```markdown
# <slug> — Domain Brief

**Created:** YYYY-MM-DD
**Rounds:** N

## Resolved
- [Question or branch] → [Answer] — [why it matters]

## Domain Language
- [Term]: [definition agreed with the user]

## Open
- [Unresolved branch] — [why still open]
```

## Guardrails

- Never ask more than 5 questions in a single round — batches beyond that defeat the purpose and read as an interrogation, not an interview.
- Never re-ask a branch already resolved in an earlier round.
- Never let the Architect start planning while a "missing information" or "risk confirmation" branch is still open — those must close first. "Approach choice" and "minor ambiguity" branches may stay open; the Architect can default them.
