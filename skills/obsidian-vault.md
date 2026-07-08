---
shortDescription: Saves task records, problems, and solutions to the Obsidian vault (Cerebro).
usedBy: [maestro]
version: 1.0.0
lastUpdated: 2026-06-26
---

## Purpose

Persist every completed task, bug fix, script, decision, concept explained, technique discovered, or pattern identified to the Obsidian vault (cerebro-vault). This ensures knowledge built with the agent is never lost between sessions and is searchable from Obsidian. Save learnings proactively — the user should never need to ask.

## Vault Path

Machine-agnostic — do not hardcode a path. Resolve it at runtime:

```bash
VAULT=$(cat ~/.claude/.vault-path 2>/dev/null)
```

`~/.claude/.vault-path` is written once per machine by `scripts/setup-claude.sh` (run after cloning cerebro-vault on a new machine). If the file is missing, run that script first.

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

2. **Find related notes.** Before building the note, scan the destination folder and sibling folders for existing notes about the same project, feature, or component:
   - List files in `02-Empresas/[Empresa]/Problemas/`, `02-Empresas/[Empresa]/Projetos/`, etc.
   - Match by project name, feature name, or shared keywords in filenames.
   - Collect all matches as `[[filename-without-extension]]` links.
   - If the current task is a fix, feature, or refactor — it should link to prior notes about the same component.

3. **Build the note.** Use the template below. File name: `YYYY-MM-DD-slug-do-problema.md`.
   - Fill `related:` frontmatter with matched `[[links]]` from step 2.
   - Add a `## Notas Relacionadas` section at the bottom listing the same links with a one-line description of why they're related.
   - Inside the note body, use `[[link]]` inline where relevant (e.g. "Este fix complementa [[2026-06-10-bug-autenticacao]]").

4. **Update existing related notes.** For each note found in step 2 that does NOT already link back to this new note — append the new note's link to its `related:` frontmatter.

5. **Write to vault.** Create the file at the resolved path. If the directory does not exist, create it.

6. **Report.** Tell the user: `Salvo em 02-Empresas/Sinaf/Problemas/2026-06-26-nome.md` and list related notes found.

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

## Notas Relacionadas
- [[nota-relacionada]] — <por que está relacionada>
```

## Sync (obrigatório após salvar)

Após criar o arquivo no vault, executar:

```bash
# Detecta vault path dinamicamente — funciona em qualquer máquina
# (setup-claude.sh grava esse arquivo na primeira vez que roda em cada máquina)
VAULT=$(cat ~/.claude/.vault-path 2>/dev/null)

if [ -z "$VAULT" ]; then
  echo "~/.claude/.vault-path não encontrado. Rode scripts/setup-claude.sh nesta máquina primeiro." >&2
  exit 1
fi

cd "$VAULT"
git add -A
git commit -m "vault: <título-da-nota>"
git push
```

Substituir `<título-da-nota>` pelo nome do arquivo criado (sem extensão).

Isso garante que o vault é atualizado no GitHub e todas as máquinas recebem as mudanças via pull automático do obsidian-git.

## Guardrails

- Never save credentials, tokens, or secrets to the vault.
- If the task was purely conversational (no problem solved, no code written), skip saving unless the user asks.
- Always use the frontmatter exactly as specified — Dataview queries depend on it.
- Scripts and SQL go in the `## Scripts / Código` section with proper fenced blocks, not as raw text.
- Always run the Sync block after saving — never save without pushing.
