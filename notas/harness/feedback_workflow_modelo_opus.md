---
name: feedback-workflow-modelo-opus
description: "Workflow/Agent sem model explícito HERDA Opus e queima o pool do usuário — a doc da ferramenta diz \"omita\", a regra do usuário diz \"sempre sete\". Errei 2x no mesmo dia."
metadata: 
  node_type: memory
  type: feedback
  modified: 2026-09-15T15:16:13.414Z
---

**Toda chamada `agent()` do Workflow tool e todo subagente do Agent tool levam
`model: 'sonnet'` (ou `'haiku'`) EXPLÍCITO. Sem exceção, sem "só esse é complexo".**

**Why:** a regra já existe na cinta (`CLAUDE.md`: *"omitir HERDA meu Opus → fan-out de
N Opus dreno do meu pool à toa"*) e mesmo assim eu errei **duas vezes em 14-15/09**.
Na segunda, um workflow de importação rodou **15 agentes Opus / 2,1 milhões de tokens**
e o autor matou no meio: *"WORKFLOW DE OPUS???? to rico não irmão, pra isso é sonnet"*.

🔴 **A causa não é esquecimento — é COLISÃO DE DOC.** A skill `workflow-authoring`, que
eu leio imediatamente antes de escrever o script, diz textualmente:

> *"opts.model … **Default to omitting it** — the agent inherits the main-loop model,
> which is almost always correct."*

Eu leio isso a 10 linhas de escrever o `agent()`, e a regra do usuário está no CLAUDE.md
carregado no logon, longe do ponto de decisão. A doc mais recente e mais próxima ganha.
**Quando duas fontes colidem, a do dono do pool manda** — a doc da ferramenta não sabe
que a quota é dele.

🛠️ **FIX ESTRUTURAL (21/09/2026) — o modelo saiu da CHAMADA e foi pro ARQUIVO.** Enquanto o
`model` fosse algo que eu digito a cada `agent()`, a doc mais próxima ia continuar ganhando
(band-aid). Agora existem papéis em `~/.claude/agents/`: **`scout`** (`model: haiku`, read-only,
acha/extrai/confere) · **`worker`** (`model: sonnet`, escopo fechado já decidido) ·
**`smart-worker`** (`model: sonnet`, `effort: high`, ambíguo/difícil, obrigado a medir).
Chamando pelo papel, **omitir vira seguro** em vez de caro — o default deixa de ser "herda Opus".
⚠️ Papel novo só fica chamável na **sessão seguinte** (o registro de agentes carrega no boot);
testado 21/09, `subagent_type: 'scout'` deu *"Agent type not found"* na mesma sessão que criou.

**How to apply:**
1. Chamar pelo PAPEL (`subagent_type: 'scout'|'worker'|'smart-worker'`). Só quando não couber em
   papel nenhum é que o `model` volta a ser digitado — e aí entra junto com o `label`, mesma linha,
   mesmo gesto. Se não sei qual, é `'sonnet'`.
2. Antes de disparar `Workflow`, conferir: `grep -c "model:" <script>` tem que bater
   com o número de chamadas `agent()`. Zero = não dispara.
3. Haiku = extrair/classificar/lookup. Sonnet = julgamento leve, pesar fonte, portar
   código com verificação. **Opus/Fable em subagente só com OK explícito do usuário** (regra de custo do autor: o pool é dele).
4. Isso vale pro Agent tool também, não só pro Workflow.

Relacionado: [[feedback_modelo_tier_policy]] · [[feedback_orquestracao_multi_llm]] ·
[[feedback_custo_mora_no_contexto]]
