---
shortDescription: Disciplined root-cause loop for bug fixes — reproduce, isolate, confirm cause, then fix.
usedBy: [coder]
version: 0.1.0
lastUpdated: 2026-08-06
---

## Purpose

A bug report names a symptom, not a cause. The Coder's default playbook assumes the fix location is already known; a bug task usually doesn't have one. Patching the reported call site fixes the symptom and leaves the root cause live for every other caller of the same shared code. This skill forces reproduction and root-cause isolation before a single line of the fix is written.

## Procedure

1. **Reproduce.** Find or write the smallest failing case that demonstrates the bug — an existing test, a new test, or a manual repro command. If it cannot be reproduced, stop and report what was tried. Do not guess at a fix for a bug that cannot be triggered on demand.

2. **Isolate.** Trace backward from the failure to the first point where behavior diverges from expectation. Grep every caller of the suspected function or module — the ticket names one symptom at one call site, but a shared function has multiple callers, and siblings may already be broken the same way even if no one filed a ticket for them yet.

3. **Form and confirm the hypothesis.** State the suspected root cause in one sentence. Confirm it — a log line, a breakpoint, or a targeted assertion that proves the hypothesis — before touching production code. A fix built on an unconfirmed guess is a second bug waiting to happen.

4. **Fix at the root.** Apply the fix at the shared point found in step 2, not at the symptom's call site, unless the shared fix is provably unsafe for one specific caller — document that exception if so.

5. **Add the regression check.** The reproduction case from step 1 becomes a permanent test. It must fail on the pre-fix code and pass after.

6. **Resume the Coder playbook.** Continue from playbook step 4c (write code, update the to-do, self-review) with the confirmed root cause as context.

## Guardrails

- Never patch the first line a debugger stops on without confirming it is the root cause, not a downstream symptom.
- Never skip reproduction because the fix "looks obvious" — unreproduced obvious fixes are the most common source of reopened tickets.
- Never fix only the caller named in the ticket when the same broken function has other callers. Fix once, where every caller routes through.
