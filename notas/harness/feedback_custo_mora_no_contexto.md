---
name: feedback_custo_mora_no_contexto
description: "quando recall: por que minha sessão gastou tanto token? onde mora o custo de uma sessão agêntica — no output ou no contexto? adianta encurtar o que o modelo escreve? quanto vale minha assinatura Max 5x em tokens? como MEDIR o consumo real das minhas sessões passadas? quanto custou de verdade aquele trabalho?"
metadata: 
  node_type: memory
  type: feedback
  modified: 2026-09-22T18:25:59.761Z
---

# O custo de uma sessão mora no CONTEXTO, não no output

Medido em 13/08/2026 sobre **136 sessões reais** (812 MB de transcript, jul–ago) e sobre a sessão
que produziu 9 sites para um cliente (`<cliente-J>`).

## O número que muda a decisão

Sessão dos 9 sites: **US$ 669** em preço de API. Onde esse dinheiro estava:

| componente | % do custo |
|---|---|
| cache **write** (contexto entrando) | **63,8%** |
| cache **read** (contexto sendo relido) | **30,4%** |
| **output — o que o modelo entrega** | 🔴 **5,8%** |

880 turnos, **contexto médio de 540 mil tokens por turno**, pico de 981 mil.

**Why:** o custo ≈ turnos × tamanho do contexto. O que o modelo escreve é a fatia menor. Por isso
otimização que ataca o output (template, "seja conciso", gerar menos HTML) rende ~6%, enquanto
cortar TURNOS e ENCHIMENTO DE CONTEXTO rende multiplicativo. Numa sessão de site, quem enche o
contexto é o loop: mockup → render → screenshot → comparar → corrigir → reler o arquivo inteiro.

**How to apply:**
- Antes de propor economia, **pergunte onde está o custo**. Se a resposta for "no output", quase
  sempre está errada.
- As duas alavancas reais: **menos turnos** (evitar loop de correção) e **modelo mais barato**
  (divide os três componentes de uma vez, não só o último).
- Imagem é o maior inflador de contexto: uma sessão com 173 screenshots é o que produz contexto
  médio de meio milhão de tokens.
- 📌 **No plano de assinatura o gargalo NÃO é dinheiro, é COTA.** Nas 136 sessões o autor consumiu
  ~US$ 7.484 de preço-API contra ~US$ 100 de mensalidade — ~75× o valor pago. Ele não paga por
  token; ele paga por não conseguir trabalhar quando bate o limite. Então o ROI de qualquer
  otimização se mede em **"quantos entregáveis cabem numa janela de 5h"**, não em dólar.

## A Anthropic não publica o plano em tokens — meça o seu

Conferido em 13/08 na página do plano e na de limites: só falam em "5× o Pro" e "medido por
complexidade". **Não existe resposta oficial** pra "minha janela comporta X tokens". O caminho é
medir o histórico local.

## Pegadinha + como conferir

- Os transcripts ficam em `<pasta-do-usuario>\.claude\projects\<projeto>\*.jsonl`. Cada linha tem
  `message.usage` com `input_tokens`, `cache_creation_input_tokens`, `cache_read_input_tokens`,
  `output_tokens` e `message.model`.
- **`input_tokens` sozinho engana**: numa sessão longa ele fica minúsculo porque quase tudo vira
  cache read. O tamanho real do contexto por turno é a **soma dos três** de entrada.
- Preço-API para converter (USD por 1M, entrada/saída) — ⏳ conferido 22/09/2026, preço muda: **Opus 5.5 `4/20`** · Opus 5 `5/25` · Sonnet 5 `3/15` · Haiku 4.5 `1/5`.
  Cache write custa **1,25× a entrada**. Cache read era **0,1×**. ⚠️ **No Opus 5.5 é 0,05× (US$ 0,20)**, então a razão já não vale pra todo modelo: confira a do modelo antes de converter.
- **Conferir:** varra os `.jsonl`, agregue por sessão e por tier, e compare `output` contra
  `cache_write + cache_read`. Se o output passar de ~15% do custo, a sessão é curta ou a medição
  está errada. Script de referência ficou em `scratchpad/medir_tokens.py` desta sessão.
- 🧲 **Delegar edição de arquivo que EU escrevi ou li não tira ele do meu contexto** (medido 16/09): o harness
  vigia o que eu toquei e, quando o executor externo (agy) edita, **injeta o diff truncado (~190 linhas) no meu
  próximo turno** — aconteceu a cada rodada com `Peca.tsx`/`Armacao.tsx`. ⇒ Em delegação longa, entrego por
  BRIEF (caminho + especificação), não escrevo stub/arquivo-base que o executor vai reescrever; e não dou `Read`
  em arquivo do executor sem motivo — confiro por render/medição.
- ⚠️ **Subagente relatando os próprios bytes não é medição** — nesta sessão um executor reportou
  `bytes_antes: 802.496` para um arquivo de 37.570 bytes (mediu a pasta). Número de custo vem do
  transcript, não do relatório do agente.

## 📐 A régua de delegação: compare AMORTIZADO, nunca bruto (21/09/2026, revisada pelo Codex no mesmo dia)

O gasto bruto de um subagente assusta e engana. Medido: `scout` 2 tool calls = **24.985** tokens ·
`scout` 24 tool calls = **63.175** · `pesquisador` lendo 9 docs de cliente (234 KB ≈ **67k tokens**)
e devolvendo ~6k de relatório = **228.985**. Parece caríssimo — até lembrar a tese desta nota: se
**eu** ler os 67k, eles ficam no meu contexto e são **relidos a cada turno**.

> **Ponto de equilíbrio em TURNOS:**
> `T > custo_do_agente ÷ (tokens_do_corpus − tokens_do_relatório)`
> No preflight da `<cliente-farmácia>`: `229k ÷ (67k − 6k) = 3,75 turnos`.
> ⚠️ O denominador é o corpus **menos** o relatório — o retorno do agente também fica no meu
> contexto e também é relido. A 1ª versão desta régua dividia por 67k e **subcontava o retorno**
> (achado do Codex, 21/09). Dá no mesmo ~4 turnos por coincidência aritmética, não por acerto.

**A pergunta de triagem: vou usar esse contexto por mais de ~4 turnos?** Sim → delega. Não → faço eu.

### Os três guarda-corpos que o Codex derrubou da v1

1. 🔴 **A régua dos turnos governa contexto ESTÁVEL (corpus, documentos) — ela NÃO veta o eixo SUJA.**
   Stack trace de 900 linhas para uma decisão única responde "não uso por 4 turnos", e a v1 mandaria
   eu comer o ruído. Errado: ali o dano não é turno futuro, é **poluição e fadiga agora**. Em conflito,
   **SUJA vence a contagem de turnos**.
2. 🔴 **Piso de overhead vem ANTES dos gatilhos.** Modelo de custo do subagente (N=2, hipótese):
   **piso ≈ 21,5k tokens + ~1,7k por tool call**. Se o trabalho inteiro (ler + fazer) couber abaixo do
   piso e não for sujo, **faço eu** — mesmo passando em "FECHADA" ou em "10+ arquivos". Contraexemplos
   do Codex: trocar uma chave num JSON de 20 linhas; 12 arquivos minúsculos somando 4k.
3. 🔴 **O "5× a favor de delegar" é PROXY, não medição.** Os números (2,01M contra 0,409M = 4,91×) só
   viram "5× de cota" se a cota contar token de Opus, Sonnet e Haiku **1:1** — e a Anthropic diz
   "medido por complexidade", o que sugere o contrário (ver seção acima). Use como ordem de grandeza.

### Em DÓLAR a vantagem é bem menor — ~1,6×, não 2–4× (corrigido pelo Codex, hard stop)

```
eu lendo 67k, 30 turnos (Opus): write 67k×5/M×1,25 = $0,419 · read 29×67k×5/M×0,1 = $0,972 → $1,39
delegando: subagente Sonnet ≈ 223k in ×3/M + 6k out ×15/M = $0,759
           + eu lendo o relatório 6k: $0,038 write + $0,087 read = $0,125 → $0,884
vantagem em preço de API = 1,39 ÷ 0,884 ≈ 1,57×
```
Eu havia escrito "2–4×" por estimativa, sem calcular o lado delegado com o relatório dentro.
⚠️ A medição agregada (228.985) **não separa input/output/cache** — se parte do gasto do subagente
for cache read, a vantagem sobe. O 1,6× é o piso pessimista. **Como o gargalo do autor é cota e não
dólar, a régua que decide é a de turnos; a de dólar existe só para não inflar a conversa.**

**How to apply:**
- ⚠️ **Brief largo infla o agente:** naquele run, 2 dos 9 arquivos (75 KB) não decidiam nada — porque
  pedi "o que já existe pra reaproveitar" em aberto. Brief estreito = o que a peça do dia exige.
- 📉 **Relatório enxuto vale mais do que parece:** cada **1k** que o agente me devolve vira **30k** de
  contexto numa sessão de 30 turnos. Literal no que decide (regra, hex, CTA, proibição), ponteiro no
  resto.
- 🧪 **N=1 por medição.** Isso não autoriza afirmar média, variância, taxa de falha nem custo marginal
  estável. É hipótese operacional, e a regressão do piso vem de 2 pontos só.

## Relacionado
[[feedback_orquestracao_multi_llm]] · [[feedback_modelo_tier_policy]] · [[project_sites_cliente_J]] (nota não incluída) · [[reference_nvidia_nim]] (nota não incluída)
