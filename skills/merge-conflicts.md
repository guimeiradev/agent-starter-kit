---
shortDescription: Resolve merge conflicts hunk-by-hunk by intent, not by picking a side wholesale.
usedBy: [coder]
version: 0.1.0
lastUpdated: 2026-08-06
---

## Purpose

A conflict marker shows two diffs, not two intents. Taking "ours" or "theirs" for a whole file silently drops one side's intent even on hunks that don't visually overlap. This skill treats each hunk as a small merge decision: understand what each side was trying to accomplish, then write the version that satisfies both.

## Procedure

1. List every conflicted file: `git diff --name-only --diff-filter=U`. Work one file at a time.

2. For each conflict hunk, read the commit history behind both sides (`git log -p` scoped to those lines) to understand intent — what each side was trying to accomplish, not just what the diff text shows.

3. Classify the hunk:
   - **Non-overlapping intents** — both changes are needed. Combine them.
   - **Same intent, different implementation** — keep the version matching current codebase conventions; discard the other cleanly, no dead leftovers.
   - **Genuinely conflicting intent** — the two sides disagree on behavior. Stop. Do not guess — escalate with both intents stated plainly (uses: `skills/agent-decision.md`, missing-information branch).

4. After resolving every hunk in a file, remove the conflict markers, then read the whole file once top to bottom. Hunk-by-hunk resolution can still leave the file structurally inconsistent.

5. Run the affected test suite before considering the file resolved.

## Guardrails

- Never run `git checkout --ours` / `--theirs` across a whole file. Always resolve per-hunk by intent.
- Never resolve a conflict by dropping one side's change because it looked smaller or older.
- Never mark a file resolved without running its tests.
