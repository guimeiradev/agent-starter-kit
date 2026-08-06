---
shortDescription: Continuous skill-improvement observation loop — logs friction/patterns during work for later review.
usedBy: [maestro]
relatedTo: [one-skill-to-rule-them-all]
version: 0.1.0
lastUpdated: 2026-08-06
---

## Purpose

Maestro's `skills/agent-memory.md` captures session/long-term memory (preferences, corrections, decisions) but has no mechanism for noticing when a *skill itself* is missing, wrong, or overbuilt. Task-observer closes that gap: throughout a session, silently log friction, corrections, and recurring patterns worth turning into a new skill or improving an existing one, without breaking flow to act on them mid-task. Full protocol lives in the vendored skill at `~/.claude/skills/task-observer/SKILL.md` (source: rebelytics/one-skill-to-rule-them-all) — this file is the integration layer, not a duplicate of that protocol.

## Procedure

1. **Session start.** If `.memory/skill-observations/log.md` does not exist under the project's `.memory/` directory (`skills/agent-memory.md`), create it using the log template from `~/.claude/skills/task-observer/SKILL.md`. This piggybacks on the existing `.memory/` root Maestro already maintains — do not create a second memory root.
2. **During the session.** Apply the full observation protocol from `~/.claude/skills/task-observer/SKILL.md` (What to Watch For, How to Log, numbering discipline). Log silently; never interrupt dispatch to act on an observation.
3. **Feed strong signals into long-term memory too.** When an observation also matches `skills/agent-memory.md` step 4's signal tiers (explicit statement or correction), record it in `.memory/long-term.md` as usual — the two logs serve different purposes (skill-authoring candidates vs. cross-session preferences/rules) and a strong-signal item can belong in both.
4. **Surfacing.** At end of session, per the vendored protocol's Surfacing Protocol: grouped summary, log-and-defer by default. Only act in-session on an explicit user request or a skill actively producing wrong output.

## Guardrails

- Do not let this skill duplicate `skills/agent-memory.md` — task-observer is for *skill-quality* signals (a skill is missing, wrong, or should be simplified), agent-memory is for *project/user* signals (preferences, decisions, discovered issues). When a signal is genuinely both, log it in both places rather than picking one.
- Never let sub-agents write to the observation log directly — same rule as `skills/agent-memory.md`: only Maestro writes memory/log files; sub-agents return findings in their handoff.
