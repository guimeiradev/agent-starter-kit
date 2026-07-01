---
shortDescription: Loads project preferences and coding standards for quick, hands-on sessions.
usedBy: [user]
version: 0.2.0
lastUpdated: 2026-07-01
copyright: Northon Torga © 2026. All Rights Reserved.
---

> **LANGUAGE REQUIREMENT**
> All thinking, reasoning, analysis, and output MUST be in **English only**.

## Purpose

Lightweight session boot for quick fixes/features. Loads project standards without orchestration overhead.

**Never commit without explicit user authorization.**

## Procedure

1. **Setup.** Ensure `.memory/` exists and is in `.gitignore`. Create subdirectories: `plan/`, `todo/`, `reviews/`. If `.gitignore` does not exist, create it. Append `.memory/` if missing.

2. **Load the rules.** Read all files under `rules/commandments/`, `rules/edicts/`, and `rules/counsel/`. State: "Rules loaded." If any directory does not exist, skip it and note the gap in delivery.

3. **Orient.** Read the top 3 `.context.md` files for directories you will touch, then `docs/FEATURE-MAP.md` if it exists, then `skills/code-arch/` if it exists (read the relevant architecture skill). Stop after reading these. If a referenced file does not exist, skip it and note the gap in delivery.

4. **State intent.** Before coding, state: "Goal: [what]. Files: [list]. Why: [reason]." If this reveals more than 5 files or touches more than 2 architectural layers, create a plan (Step 4a). Otherwise, proceed to Step 5.

   4a. **Plan.** Create `.memory/plan/YYYY-MM-DD-<prefix>-<slug>-v0.md` with this schema:
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

5. **Track progress.** If the task requires 3 or more separate file edits or commands, create a to-do (uses: `skills/task-tracking.md`). For single-file fixes, skip this.

6. **Code.** Before writing, read `.context.md` for the target directory (if it exists) and examine the 2 files in the same directory with the most recent git commits. When a plan includes test specs, write tests first — they must fail before implementation.

7. **Maintain context.** After coding, run `git status` (if not in a git repo, skip this step). If output includes new directories or deleted files, follow the context maintenance skill (uses: `skills/context-maintenance.md`).

8. **Self-review.** Dispatch a reviewer sub-agent (follows: `skills/dispatch.md`, uses: `personas/reviewer.md`) to review your changes. If sub-agent dispatch is not available, review your changes against the code review procedure (uses: `skills/code-quality-review.md`) and the reviewing edict (follows: `rules/edicts/code-quality.md`). If the reviewer finds substance issues (architecture violations, missing error paths, broken contracts), fix them before proceeding.
   - **Substance** — architecture violations, missing error paths, broken contracts.
   - **Form** — naming, style, consistency with surroundings.
   - **Prose** — instructions and descriptions must use concrete conditions instead of subjective qualifiers. Dense lines must be broken into sub-items.

9. **Deliver.** Run the project's test command. If unknown, check for a Makefile, package.json scripts, or go.mod to determine it. If no tests exist, state this. If tests fail and cannot be fixed, state what failed and why. Summarize: what changed, what decisions were made, what skills were missing (if any), and anything left incomplete.

## Guardrails

- This skill does not override rules or commandments — it indexes them. If a rule says MUST, it still means MUST.
