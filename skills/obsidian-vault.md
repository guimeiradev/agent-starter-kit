---
shortDescription: Saves task records, problems, and solutions to the Obsidian vault (Cerebro).
usedBy: [maestro]
version: 1.0.0
lastUpdated: 2026-06-26
---

## Purpose

Persist every completed task, bug fix, script, decision, concept explained, technique discovered, or pattern identified to the Obsidian vault at `/opt/obsidian-vault/Cerebro/`. This ensures knowledge built with the agent is never lost between sessions and is searchable from Obsidian. Save learnings proactively — the user should never need to ask.

## Vault Path

```
/opt/obsidian-vault/Cerebro/
```

## Context → Folder Mapping

Infer the destination from the current working directory, task description, or user's words:

| Signal | Vault folder |
|---|---|
| CWD or task contains `sinaf`, `infobase`, `tec software` | `02-Empresas/Sinaf/Problemas/` |
| CWD or task contains `hcor` | `02-Empresas/Hcor/Problemas/` |
| CWD or task contains `bnp`, `paribas` | `02-Empresas/BNP-Paribas/Problemas/` |
| Scripts / SQL used in company context | `02-Empresas/[Empresa]/Scripts/` |
| Studys, courses, general learning | `03-Pessoal/Estudos/` |
| Personal projects | `03-Pessoal/Projetos/` |
| Generic technical concept (no company context) | `04-Resources/Aprendizados/` |
| Concept / technique / pattern with company context | `02-Empresas/[Empresa]/Aprendizados/` |
| **Unclear** | **Ask the user before saving** |

## Procedure

1. **Infer context.** Check CWD (`pwd`), task description, and conversation for company signals using the mapping above. If ambiguous, ask: *"Onde salvo no vault — Sinaf, Hcor, BNP ou Pessoal?"*

2. **Build the note.** Use the template below. File name: `YYYY-MM-DD-slug-do-problema.md`.

3. **Write to vault.** Create the file at the resolved path. If the directory does not exist, create it.

4. **Report.** Tell the user: `Salvo em 02-Empresas/Sinaf/Problemas/2026-06-26-nome.md`

## Note Template

```markdown
---
title: "<title>"
date: YYYY-MM-DD
empresa: Sinaf | Hcor | BNP-Paribas | Pessoal
tipo: bug | feature | infra | banco-de-dados | config | estudo | aprendizado
status: resolvido | em-andamento | bloqueado
tags: [#empresa/sinaf, #tipo/problema, #dev/backend]
related: []
---

## Contexto
<what was the situation / what project>

## Problema
<what was wrong or what needed to be done>

## Investigação
<what was tried, what was discovered>

## Solução
<what resolved it>

## Scripts / Código
<SQL, bash, code snippets used — use fenced code blocks>

## Referências
<links, docs, tickets>
```

## Guardrails

- Never save credentials, tokens, or secrets to the vault.
- If the task was purely conversational (no problem solved, no code written), skip saving unless the user asks.
- Always use the frontmatter exactly as specified — Dataview queries depend on it.
- Scripts and SQL go in the `## Scripts / Código` section with proper fenced blocks, not as raw text.
