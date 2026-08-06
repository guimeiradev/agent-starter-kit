---
shortDescription: Use graphify's knowledge graph instead of re-walking the tree for large/unfamiliar codebases.
usedBy: [contextualizer]
relatedTo: [graphify]
version: 0.1.0
lastUpdated: 2026-08-06
---

## Purpose

Contextualizer's default Context Scan (`personas/contextualizer.md` step 2) walks the directory tree recursively on every invocation. On a large or unfamiliar codebase this re-reads a lot of files that graphify has already indexed. Graphify (`~/.claude/skills/graphify/SKILL.md`) builds a persistent, queryable knowledge graph (`graphify-out/graph.json`) once, then answers structural questions via BFS/DFS traversal instead of raw file reads — cheaper in tokens for repeat context work on the same project.

## Procedure

1. **Check for an existing graph first.** Before walking the tree, check whether `graphify-out/graph.json` exists at the project root. If it does, prefer it over a fresh recursive walk for Structural Brief and Review Scoping modes (`personas/contextualizer.md` steps 5–6): run `graphify query "<question about the relevant module/feature>"` to pull the needed structural context instead of re-reading files.
2. **No existing graph, large codebase.** If no graph exists and the codebase is large enough that a full recursive walk would be expensive (many files/directories, unfamiliar structure), run `graphify <path>` once to build it, then use `graphify query`/`graphify path`/`graphify explain` for the rest of the task. Skip this for small/simple projects — a direct walk is cheaper than building a graph you'll only query once.
3. **Full Context Scan mode (default, step 2-4 of the persona) still writes `.context.md` and `docs/FEATURE-MAP.md` as normal.** Graphify is a research aid for gathering the material faster, not a replacement for those deliverables — the handoff format is unchanged.
4. **Keep the graph fresh.** If `.context.md` files are being regenerated because the project drifted (`skills/context-maintenance.md`), also run `graphify <path> --update` so the graph doesn't go stale alongside them.

## Guardrails

- Never treat a stale `graphify-out/graph.json` as ground truth for a project that has changed significantly since it was built — check the graph's age/relevance before trusting it over a fresh read.
- Graphify is an optimization for the Contextualizer persona only. Do not route other personas' file-discovery needs through it — they use `Explore`/`Grep`/`Glob` directly per their own playbooks.
