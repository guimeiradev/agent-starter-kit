---
shortDescription: Loads project preferences and coding standards for quick, hands-on sessions.
usedBy: [user]
version: 0.3.0
lastUpdated: 2026-07-09
copyright: Northon Torga © 2026. All Rights Reserved.
---

> **LANGUAGE REQUIREMENT**
> All thinking, reasoning, analysis, and output MUST be in **English only**.

## Purpose

Lightweight session boot for quick fixes/features. Loads project standards without orchestration overhead.

**Never commit without explicit user authorization.**

## Procedure

1. **Setup.** Ensure `.memory/` exists and is in `.gitignore`. Create subdirectories: `plan/`, `todo/`, `reviews/`. If `.gitignore` does not exist, create it. Append `.memory/` if missing.

2. **Load the rules.** Read all files under `rules/`. State: "Rules loaded." If the directory does not exist, note the gap in delivery.

3. **Orient.** Read `README.md` at the project root, then `docs/FEATURE-MAP.md` if it exists. Note any files or directories the README references that are relevant to the task — read those too. If a referenced file does not exist, skip it and note the gap in delivery.

4. **Track progress.** If the task requires 3 or more separate file edits or commands, create a to-do (uses: `skills/task-tracking.md`). For single-file fixes, skip this.

5. **State intent.** Before coding, state: "Goal: [what]. Files: [list]. Why: [reason]." If this reveals more than 5 files or touches more than 2 architectural layers, create a plan (Step 5a). Otherwise, proceed to Step 6.

   5a. **Plan.** Create `.memory/plan/YYYY-MM-DD-<prefix>-<slug>-v0.md` with this schema:
   ```markdown
   # [Feature Name]

   ## Goal

   [Problem statement. Why this matters. What success looks like.]

   ## Information Flow

   1. `path/to/entry.ext` — what happens here
   2. `path/to/service.ext` — what happens here
   3. `path/to/repository.ext` — what happens here

   ### Phase 1: [Name] — PENDING

   **Before:** [current state]
   **After:** [target state]

   **Methods:**
   - `functionName(param: Type): ReturnType` — purpose

   **Files:**
   - `path/to/file.ext`

   **Reference files:**
   - `path/to/similar.ext` — for style matching

   **Acceptance criteria:**
   - [ ] [testable condition]

   **Tests (if applicable):**
   - Good: [scenario]
   - Bad: [scenario]
   - Ugly: [scenario]
   ```
   Mark inapplicable sections as "N/A". On revision, increment version (`v0.md` → `v1.md`). Do not code until user approves. For ambiguous task intent (not scope), read and follow `skills/agent-decision.md`.

6. **Code.** Read and follow `skills/dispatch.md` to dispatch `personas/coder.md` for the implementation. If sub-agent dispatch is not available, implement directly: read `.context.md` for the target directory, examine sibling files for style, write tests first when the plan includes specs, and run the full test suite. Consult `rules/code/` for language-specific conventions.

7. **Maintain context.** After coding, run `git status` (if not in a git repo, skip this step). If output includes new directories or deleted files, follow the context maintenance skill (uses: `skills/context-maintenance.md`).

8. **Self-review.** Run `git diff HEAD --shortstat` and `git ls-files --others --exclude-standard | xargs wc -l` to check LOC changed.
   - Read and follow `skills/dispatch.md` to dispatch `personas/reviewer.md` for the changes. If sub-agent dispatch is not available, review your changes following `skills/code-coherence-review.md`, `skills/code-quality-review.md`, `skills/code-sec-review.md`, and the reviewing rule (follows: `rules/code/quality.md`).
   - Incorporate the reviewer's findings before proceeding.

9. **Deliver.** Run the project's test command. If unknown, check for a Makefile, package.json scripts, or go.mod to determine it. If no tests exist, state this. If tests fail and cannot be fixed, state what failed and why. Summarize: what changed, what decisions were made, what skills were missing (if any), and anything left incomplete.

## Guardrails

- This skill does not override rules — it indexes them. If a rule says MUST, it still means MUST.
