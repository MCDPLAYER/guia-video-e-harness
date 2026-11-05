---
name: reference_claude_code_hooks
description: "quando recall: que hook do Claude Code roda no FIM da sessão / antes de compactar? Qual evento INJETA contexto que o modelo lê e age (SessionStart, Stop, PreCompact, SessionEnd)? Dá pra fazer hook que força captura de memória ao fechar? type prompt/agent existe? por que /capturar é manual e não hook?"
metadata: 
  node_type: memory
  type: reference
---

# Hooks do Claude Code — quem injeta contexto, quem dispara quando

Verificado 2026-06-19 contra os docs oficiais (`code.claude.com/docs/en/hooks.md`), montando o Hermes #5 (projeto interno de setup do autor).

**Eventos que INJETAM contexto que o modelo lê e age** (via `additionalContext` no JSON, ou stdout): `SessionStart`, `UserPromptSubmit`, `UserPromptExpansion`, `PreToolUse`, `PostToolUse`, `Stop`, `SubagentStop` (+ Setup/SubagentStart/PostToolBatch). Em `SessionStart`/`UserPromptSubmit` o **stdout puro** já vira contexto (não precisa montar JSON).

**Eventos que NÃO injetam contexto:** ⛔ `PreCompact` e ⛔ `SessionEnd`.
- **SessionEnd** = cleanup-only. A sessão JÁ acabou, não há mais chamada ao modelo → **não consegue fazer o modelo capturar/agir**. Serve só pra log/limpeza. Matchers: `clear`/`resume`/`logout`/`prompt_input_exit`/`other`.
- **PreCompact** = dispara ANTES da compactação (matcher `auto`/`manual`). **NÃO injeta `additionalContext`** e o stdout NÃO vai pro contexto pós-compactação. Pode só: bloquear a compactação (exit 2) e ter efeito colateral (escrever arquivo). Provavelmente só aceita `type: command`/`mcp_tool` (não agent/prompt).
- **Stop** = dispara a CADA resposta do assistente (não só no fim da tarefa). Injeta `additionalContext`; pode forçar continuação com `decision:"block"` + `reason`. Guarda contra loop: ler `stop_hook_active` do stdin e sair cedo se `true` (cap de 8 blocks). **Ruidoso pra lembrete** (todo turno).

**Tipos de hook:** `command`, `http`, `mcp_tool`, `prompt` (manda um prompt p/ um modelo, retorna decisão sim/não — `$ARGUMENTS` = input), `agent` (spawna subagente com Read/Grep/Glob, retorna decisão — **experimental**). prompt/agent são pra VERIFICAR/decidir, não pra trabalho livre.

**Consequência (decisão Hermes #5, re-confirmada 2026-06-23 — segue 100%; descoberto que `PostCompact` existe mas é stderr-only, idem não serve):** **não existe hook que dispare no FIM da sessão E deixe o modelo agir** (SessionEnd não age; PreCompact/PostCompact não injetam; Stop é por-turno). Mas — e isso é o ponto mais forte — captura **não deveria** ser hook de qualquer jeito: é JULGAMENTO (senso crítico — band-aid≠memória, nota irmã, description que fisga recall), não ação mecânica determinística que a doc reserva pra hooks; forçar via hook geraria nota-lixo. **Política (passo 2 da revisão de boas práticas, 23/06):** captura fica **manual** (`/capturar`) **+ o Claude OFERECE proativamente** no fim de trabalho substancial — gatilho fixado na cinta (`CLAUDE.md` → Princípios → "Memória — quando e como"), pra não depender do usuário lembrar. Ver [[reference_recall_so_memory]] (nota não incluída).

**Config:** `~/.claude/settings.json` → bloco `hooks` → `{ "<Evento>": [ { "matcher": "...", "hooks": [ { "type":"command", "command":"bash '...sh'" } ] } ] }`. `Stop`/`SessionEnd` não usam matcher por ferramenta. Hook existente do autor: `PostToolUse` (Edit|Write) → `sync-skills.sh`.

🔴 **Matcher por FERRAMENTA não vê o trabalho feito por OUTRA ferramenta (21/09/2026).** Esse hook lê
`tool_input.file_path` — campo que **só existe em Edit/Write/MultiEdit**. Editando o mesmo arquivo via
**Bash** (`python`, `sed`, heredoc), que é o caminho default em bypass mode, o hook **não dispara e não
reclama**: as 3 cópias da skill dessincronizaram em silêncio e só o `md5sum` denunciou.
⇒ **Editou skill por shell = sincronize na mão e confira por hash.** Conserto estrutural seria um
gatilho que não dependa do nome da tool (incluir `Bash` no matcher e parsear o comando, ou `Stop`
varrendo a pasta de skills no fim do turno).

## Pegadinha + como conferir
- ⚠️ **NÃO confie em agente/memória pra mecânica de hook — confirme nos docs.** Nesta sessão um agente `claude-code-guide` AFIRMOU que PreCompact injeta `additionalContext`; o WebFetch do doc real **desmentiu** (PreCompact está fora da lista). Quase construí o #5 errado em cima da alucinação.
- **Como conferir:** `WebFetch` em `https://code.claude.com/docs/en/hooks.md` perguntando "quais eventos suportam additionalContext / qual stdout vira contexto". A tabela de eventos é a fonte; a lista de quem-injeta é o que importa.
- **Stop loop:** se for usar `decision:"block"` no Stop, SEMPRE checar `stop_hook_active` primeiro, senão trava em loop.
