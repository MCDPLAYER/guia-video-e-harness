# Histórico da pipeline-multi-llm (jun–set/2026)

> ⏳ Histórico: números, versões e incidentes de jun–set/2026. Reconferir antes de usar.
> Movido do `SKILL.md` em 30/09/2026 (prompt-audit, achados PL14 e PL18). O que sobreviveu como regra atemporal ficou no SKILL.md; aqui mora o registro datado. Modelo, versão de CLI e cota citados abaixo podem estar mortos.
> Enxugado na exportação: saíram os detalhes de ferramenta declarada morta (Gemini CLI, extrator `agy-extract.py`/`agy-inspect.py` que escavava o SQLite do agy, modelos Codex que hoje dão 400). Ficou a lição geral de cada incidente.

---

## Medição da régua amortizada (21/09/2026) — saiu da seção de delegação (PL18)

Regra que ficou no SKILL.md: a fórmula `T > custo_do_agente ÷ (corpus − relatório)`, o piso de overhead e os guarda-corpos 1 e 2. Abaixo, o que era registro do run.

📏 **A RÉGUA É AMORTIZADA, não bruta — e passou pelo Codex em 21/09, que derrubou 3 coisas da v1.** O gasto bruto assusta e engana: `scout` 2 tool calls = 24.985 tokens · 24 tool calls = 63.175 · `pesquisador` lendo 9 docs (234 KB ≈ 67k) = 228.985, devolvendo ~6k. Mas se EU ler os 67k, eles são relidos a cada turno.

> `T > custo_do_agente ÷ (corpus − relatório)` → `229k ÷ (67k − 6k) = 3,75 turnos`.
> ⚠️ O denominador desconta o relatório — ele também fica no meu contexto. A v1 dividia por 67k e subcontava o retorno.

**Os 3 guarda-corpos (achados do Codex, todos acatados) — o 3º saiu do SKILL.md:**

3. 🔴 **"5× mais barato" é PROXY.** 2,01M contra 0,409M = 4,91× só vira "5× de cota" se a cota contar token de Opus/Sonnet/Haiku 1:1 — e a Anthropic fala em "medido por complexidade". Em **dólar** a vantagem é **~1,6×** (`$1,39 ÷ $0,884`), não os 2–4× que eu tinha estimado sem fazer a conta. Como o gargalo aqui é cota, a régua que decide é a de turnos.

⚠️ **Brief largo infla o agente:** naquele run pedi "o que já existe pra reaproveitar" em aberto e 2 dos 9 arquivos (75 KB) não decidiam nada. Furei, no primeiro teste, a regra de brief estreito que eu tinha acabado de escrever.

---

## Aprendizados maio/2026

- Usar subagent-crítico como contraponto deliberado, não só como confirmador educado.
- Receitas `R1-R5` viram playbooks reaproveitáveis; nomear a receita reduz reinvenção da escada.
- `thinking_budget` desligado entregou ganho operacional de ~3x quando profundidade extra não mudava o resultado.
- `agy` (Antigravity) funciona headless via stdin e encaixa bem em automação.

## Aprendizados junho/2026 (sessão 2026-06-11 — redesenho de eficiência com o autor)

- **Gemini CLI morreu** (sunset 18/06/2026 no plano AI Pro) → `agy` virou o sucessor compulsório do Tier 1.
- **T1.5 novo:** Sonnet 4.6 (Thinking) via agy = executor agentic-leve na cota Google ociosa. Resolve o antigo dilema "Gemini peca em agentic mas Max é apertado".
- **Faixas de aceitação substituem "critério baixo/alto" pro Codex:** régua = verificabilidade + raio de explosão (verde mecânico / spot-check / gate T3). Supervisão cobre assimetria de CONTEXTO, não de inteligência.
- **Crítico nunca é outro Claude** — virou regra dura com a chegada dos Claudes "grátis" no agy.
- **Triagem agora é auto-gatilhada** pelo CLAUDE.md global (tarefa não-trivial → classificar tier mesmo sem o usuário nomear ferramenta) e a description desta skill gatilha por FORMATO de tarefa, não só por nome.

## Aprendizados jul/2026 — digest mensal (fan-out de 167 sessões, 2026-07-01)

- **Extrator que escava o armazenamento interno de uma CLI quebra a cada update.** Enquanto o agy não tinha saída JSON, a resposta era tirada do SQLite da conversa por um script próprio; o update 1.0.7→1.0.12 trocou o campo da mensagem final pelo título da conversa, e o 1.0.16 mudou o campo do eco do prompt — duas quebras em uma semana. O conserto mais robusto foi parar de caçar número de campo (descartar a string que CONTÉM o prompt inteiro). O problema só morreu quando o agy ganhou `--output-format json` (wrapper v2). **Lição:** saída não documentada = re-validar com canário depois de todo update.
- **agy Flash era frágil no headless pra volume:** (a) **não lia arquivo** quando instruído (ficava "waiting for system-wide search of C:"); (b) prompt via `-p` **estoura a linha de comando do Windows (~32KB)**. → por isso o prompt vai sempre em arquivo via stdin (wrapper). Na época, fan-out de volume foi pro Codex (stdin).
- **Codex `--sandbox read-only` no Windows NÃO roda subprocesso** (`CreateProcessAsUserW failed: 5`) — nesse modo o Codex só analisa TEXTO passado via **stdin**, não lê filesystem nem roda python/git. Tocar arquivos exigiria `--dangerously-bypass-approvals-and-sandbox`, mas o classifier do harness bloqueava essa flag em auto mode (corrigido em 07-06, abaixo). **Padrão validado na época:** EU coleto (PowerShell read-only) e passo o conteúdo via stdin (`Get-Content lote.txt | codex exec --sandbox read-only ... -`).
- **gpt-5.4 `-c model_reasoning_effort="low"` = ~5x mais rápido** (300s→64s por lote de 200KB) SEM perder qualidade em extração mecânica schema-bound. Reasoning profundo é desnecessário pra extração — reservar pro que precisa raciocínio. (O modelo morreu; a lição de effort baixo em extração vale.)
- **gpt-5.4-mini NÃO segue instrução complexa em contexto grande** — respondeu conversacionalmente à última msg da sessão em vez de extrair. mini só pra tarefa simples; extração schema-bound = modelo cheio.
- **Cota ChatGPT Plus esgota em ~15 chamadas de ~200KB** ("You've hit your usage limit... try again at HH:MM") — fan-out grande não cabe numa janela de cota Plus.
- **Fan-out híbrido validado (padrão pra cota externa que acaba no meio):** Codex processa o volume até torrar a cota → o resto vai pra **Workflow de subagents Sonnet (lotes grandes) + Haiku (menores)** na cota Anthropic, 1 subagent por lote (não estoura contexto — até lote de 980KB o Sonnet aguentou). Distribuir por tamanho: Haiku barato nos pequenos, Sonnet nos grandes/complexos.

## Aprendizados 2026-07-06 — FIX DEFINITIVO das gambiarras Codex/agy no Windows

Reclamação recorrente do autor ("toda hora UTF, PowerShell, read-only, sandbox"). Causa raiz diagnosticada ao vivo e blindada em wrapper (deixou de ser dor de cada sessão):

- **Ambiente já melhor do que as notas diziam:** pwsh **7.6.3** já é o shell default do harness (`chcp 65001`, OutputEncoding utf-8) → metade do inferno de acento morreu no lado do Claude Code. Codex 0.125.0, agy 1.0.16.
- **A bomba do Codex era o `config.toml` compartilhado com o app desktop:** `service_tier="priority"` (a CLI só aceita fast/flex) fazia **100% das chamadas** morrerem em "Error loading config.toml". Fix definitivo: **`CODEX_HOME=~/.codex-cli` dedicado** com config mínimo limpo — o app desktop nunca mais encosta na CLI. Mata junto o gpt-5.5/xhigh lento e o ruído de plugins/memories ("no such table: jobs").
- **Sandbox no Windows (corrige nota velha):** `codex exec` IGNORA o `sandbox_mode` do config e força read-only; `--full-auto`/`--sandbox workspace-write`/`read-only` **todos caem em read-only e negam qualquer subprocesso** ("rejected: blocked by policy" — nem `python -c print(1)`). **Só `--dangerously-bypass-approvals-and-sandbox` executa.** E: o classifier do harness **não** bloqueia mais essa flag (a nota de 01/07 dizia que bloqueava).
- **`codex-run.ps1` criado** (gêmeo do `agy-run.ps1`): CODEX_HOME + auth auto-sync + prompt UTF-8 via stdin + bypass + skip-git, num comando. Validado com prompt acentuado (`manutenção/ração/avião` intactos, resposta limpa em out.md).
- **Regra operacional:** no Windows, **nunca invocar Codex/agy crus** — sempre `codex-run.ps1` / `agy-run.ps1`. Invocação crua só pra depurar.

## Aprendizados 2026-07-09 — padrão "digerir dump grande → RAG" (R6, validado com catálogo NIM)

O autor testou delegação em VOLUME: dump de 1.4MB / 25k linhas (catálogo NVIDIA NIM copiado) → stack de IA pesquisável no vault (`_ferramentas/stack-ia/`). Meu contexto **nunca leu** o dump nem os 107 model cards. Padrão que emergiu e VALIDOU:

- **Receita R6 — dump bagunçado → RAG:** (1) EU faço recon barato (grep/estrutura, achar delimitador) + escrevo parser/scripts determinísticos; (2) **NIM extrai o VOLUME** — chunk por orçamento de chars → `meta/llama-3.1-8b-instruct` via API OpenAI-compat, schema-bound (JSON estrito), checkpoint por chunk, throttle 40rpm; (3) **agy enriquece via web** com prompts PEQUENOS por categoria (a força dele); (4) **Codex critica macro** (taxonomia/claims arriscados, via stdin, gpt-5.5); (5) EU sintetizo as notas + **reconcilio com conhecimento verificado do vault** + indexo (`build_index.py`/`build_index_gemini.py`).
- **NIM 8B = extrator de volume (regra nova):** modelo QUENTE responde ~1s; **70B tem cold-start e estoura timeout** → usar 8B pra lote schema-bound (extração mecânica não precisa de 70B). Background + checkpoint por chunk = resiliente. **Complementa "agy não engole arquivo grande":** o VOLUME agora tem 2 casas — **NIM** (API, sem teto de cota, dogfood) OU **Codex** (stdin, ~15 chamadas Plus). agy fica só pro web-research de prompt curto.
- **Reconciliação > pesquisa fresca:** claims web do agy foram CORRIGIDOS por fato verificado do vault (FLUX-Kontext no NIM=demo-only; F5-TTS=não-comercial; SD3.5=download-only). Sempre cruzar pesquisa nova com memória verificada ANTES de canonizar — senão o agy vira evangelho errado.
- **agy Flash headless (wrapper) = confiável pra texto agora** (5 briefs completos em out.md); o fix de 07-06 segura. Detalhe em [[reference_gemini_codex_cli]] (nota não incluída).
- **Ratio:** NIM+agy+Codex fizeram digestão+pesquisa+crítica; meu contexto = orquestração + scripts + síntese + QA. Overhead de orquestração é real (input bagunçado exige iteração), mas << eu ler tudo. **Lean toward manager-mode em tarefa de VOLUME/DIGEST** (não em microtarefa).
