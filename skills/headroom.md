---
shortDescription: Local token-compression proxy for LLM API calls — reduces cost/context on every dispatch.
usedBy: [maestro]
relatedTo: [headroom, anthropic-api]
version: 0.1.0
lastUpdated: 2026-08-06
---

## Purpose

Headroom is a local proxy (`headroom proxy`) that sits between any client and the upstream LLM API, applying prompt compression, caching, and AST-aware code-context trimming before the request leaves the machine. Maestro dispatches many sub-agent calls per session (`skills/dispatch.md`); routing those calls through Headroom cuts token spend without changing dispatch logic — it is transport-layer, not persona-layer, so no persona or skill needs to know it exists.

## Procedure

1. **Check the proxy is up.** Run `headroom doctor`. If `proxy` shows `fail` or `not running`, start it detached: `headroom proxy --port 8787 &` (or the project's configured port). Never use `headroom wrap <tool>` for non-interactive dispatch — it launches an interactive child process and blocks.
2. **Verify routing.** `headroom doctor` should report `claude` and `shell env` as `pass` once `ANTHROPIC_BASE_URL` (or `OPENAI_BASE_URL` for OpenAI-compatible providers) is set to `http://127.0.0.1:<port>`. This is set once in `~/.claude/settings.json`'s `env` block — a host-level concern, not something Maestro re-applies per dispatch.
3. **Upstream chaining (optional).** Headroom's own upstream defaults to `https://api.anthropic.com`. It can instead point at a multi-provider gateway via `--anthropic-api-url <gateway-url>/v1`, so compression happens before multi-provider routing/fallback. Only relevant if such a gateway is installed and fully configured (logged in, 2+ providers connected) — none is currently installed in this vault.
4. **CLI dispatch calls are already covered.** Because routing is set at the `ANTHROPIC_BASE_URL` env level, `skills/dispatch.md`'s CLI Dispatch section (`claude --model [model]`, etc.) passes through Headroom automatically — no per-call flag needed.

## Guardrails

- Never run `headroom wrap` from a non-interactive context (background task, Bash tool) — it starts an interactive session and will hang the caller.
- A session already running when `ANTHROPIC_BASE_URL` is added to `settings.json` will NOT pick up the change — it takes effect on the next session start.
- If `headroom doctor` shows the proxy running but `claude`/`shell env` still warn, the env var did not apply yet — this is expected mid-session, not a bug to chase.
