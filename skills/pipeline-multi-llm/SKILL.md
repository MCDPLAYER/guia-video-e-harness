---
name: pipeline-multi-llm
description: >
  Manual operacional pra orquestrar Antigravity CLI (agy: Gemini 3.x + Claude Sonnet/Opus 4.6
  na quota Google) + Codex CLI como funcionarios do Claude, e decidir entre escada multi-LLM
  (pipeline) vs fan-out paralelo (Workflow tool).
  Use SEMPRE que mencionar: agy, antigravity cli, codex cli, delegar pra Gemini,
  rodar Codex, Tier 1 / Tier 2 / Tier 3, multi-llm, batch de pesquisa com IA externa,
  critica final com Codex/Gemini, escolher tier de uma tarefa, code review com Codex,
  varredura de codebase, erro de modelo / quota / sandbox em CLI externa, Workflow tool,
  fan-out de subagents, escolher entre pipeline e Workflow, ou hybrid Workflow + Codex.
  Use TAMBEM pelo FORMATO da tarefa, mesmo sem nomear ferramenta: lote/batch de itens
  similares independentes, varredura de pasta/vault/codebase grande, analise de video/midia gerada,
  pesquisa em volume multi-fonte, critica de peca antes de publicar/aprovar,
  definir se uma task e simples ou complexa e qual MODELO/tier ela pede (ponto de ruptura),
  que modelo dar a subagente interno (Sonnet/Haiku default vs Opus), ou usar modelo LOCAL
  (Gemma/Ollama/localai) e onde ele quebra.
---

# Pipeline Multi-LLM - Manual do Gestor

Eu (Claude) orquestro o **Antigravity CLI `agy`** (cota Google AI Pro; modelos vivos em `agy models`) e o **Codex CLI** (ChatGPT Plus; modelos em `~/.codex-cli/models_cache.json`). Cota Claude é o gargalo; número de cota se mede na hora, não se cita.

> Codex (conta ChatGPT): default `gpt-6.1-sol` (rotina `-Effort low`; pesado `high`), mais forte `gpt-6-astra`. Lista viva = tabela "Modelos" lá embaixo (20/10/2026). `gpt-5.5`/`gpt-5.4` dão 400.

**Delego quando a tarefa é fechada, suja ou varrida (gatilhos abaixo); fica comigo orquestração, decisão criativa, voz e integração cross-arquivo.**

**Postura AUTONOMA (preferência do autor, 2026-06-08 — "prefiro assim"):** ja IR FAZENDO e resolvendo. Dirigir a orquestracao ponta a ponta — decidir tier, disparar Gemini/Codex, ler, arbitrar, iterar, corrigir e ENTREGAR o resultado pronto — sem pedir permissao a cada passo. Parar so pra decisao criativa genuina ou gasto/risco real (credito, destrutivo, publicacao). Menos "posso fazer X?", mais X feito + resuminho util. Vale pra toda producao, nao so multi-LLM (espelhada em `feedback_postura_autonoma`, nota não incluída).

**Auto-suficiencia:** esta skill e canonica por si. Se memorias auxiliares nao estiverem disponiveis, sigo as regras deste proprio arquivo sem depender de contexto externo.

**Assuncao de shell:** exemplos abaixo estao em Bash. Se o host estiver em PowerShell, adaptar comandos, quoting e caminhos antes de executar. Nos blocos criticos abaixo existe pelo menos um equivalente em PowerShell.

---

## SETUP WINDOWS (ler antes de delegar)

(Os scripts `agy-run.ps1`, `codex-run.ps1` e `TESTE-DE-AMBIENTE.ps1` citados abaixo não estão incluídos neste repo.)

No Windows o Claude Code executa comando por uma de duas vias: **Git Bash** (se instalado) ou **PowerShell**. As receitas desta skill sao Bash-first; havendo Git Bash, rodam direto. Os blocos PowerShell sao o equivalente pra quando o shell for PowerShell.

**Preparar o ambiente (1x por maquina):**

1. **pwsh 7** (recomendado forte): `winget install --id Microsoft.PowerShell -e`. O Windows PowerShell 5.1 (default) quebra com acento, nao tem `&&` e estraga o redirecionamento de stderr. O pwsh 7 le UTF-8 por padrao, tem `&&`/`||` e `Out-File -Encoding utf8` sai **sem BOM** (o que Python/JSON esperam).
2. **Git Bash** (recomendado): faz as receitas Bash desta skill rodarem sem adaptacao.
3. **node 22+ / npm**: o Codex e instalado via npm global (pacote `openai/codex`, com escopo openai).
4. Rodar o **`TESTE-DE-AMBIENTE.ps1`** (nesta pasta) — confere PowerShell, Git Bash, node/npm, codex, login, PATH e faz round-trip de acento. Verde em tudo = pode delegar (o check do `gemini` é legado: o Gemini CLI morreu, vermelho ali não bloqueia).

**Regras de ouro no Windows (valem pros blocos PowerShell abaixo):**

- **Prompt sempre em arquivo + via STDIN, nunca inline em `-p`.** Prompt longo/com acento passado como argumento quebra por quoting no Windows. Os wrappers `agy-run.ps1` e `codex-run.ps1` ja fazem isso (`-PromptFile`).
- **Ler prompt em PS 5.1 SEMPRE com `-Encoding UTF8`.** Sem isso o 5.1 le como ANSI e mojibaka o acento ANTES de chegar na CLI. (No pwsh 7 nao precisa, mas nao atrapalha.)
- **Codex no Windows — usar o wrapper `codex-run.ps1` (nesta pasta), nao invocar cru.** Tres gambiarras quebram a chamada crua; o wrapper resolve as tres (validado 2026-07-06, Codex 0.125.0, pwsh 7.6.3): **(1)** o `~/.codex/config.toml` e COMPARTILHADO com o app desktop, que grava `service_tier="priority"` — a CLI so aceita `fast`/`flex`, entao TODA chamada morre em "Error loading config.toml: unknown variant priority" (+ o app enfia gpt-5.5/xhigh lento e plugins/memories que inflam). Fix: `CODEX_HOME` dedicado (`~/.codex-cli`) com `config.toml` minimo. **(2)** sandbox: `codex exec` IGNORA o `sandbox_mode` do config e forca read-only; no Windows `--full-auto`/`--sandbox workspace-write`/`read-only` NAO executam (caem em read-only e negam ate `python -c "print(1)"` com "rejected: blocked by policy"). So `--dangerously-bypass-approvals-and-sandbox` roda de fato. **(3)** encoding/quoting: prompt SEMPRE de arquivo UTF-8 via stdin (`codex exec -`), nunca inline. Invocar cru so pra depurar.
- **agy:** rodar de uma **cwd limpa** (subpasta de trabalho, `-Cd`), nunca da raiz de um drive (ex.: `C:\`) — ele varre a cwd inteira e da EPERM em pasta de sistema.

---

## PREFLIGHT MINIMO

Antes de delegar em maquina nova, sessao nova ou pasta nova:

1. `agy --version`
2. `codex --version`
3. `codex login status`
4. criar contrato de pasta `./_llm_runs/YYYY-MM-DD/`

**Contrato minimo de pasta:**

```text
./_llm_runs/YYYY-MM-DD/
  001-nome-curto/
    prompt.md
    out.md
    err.log
```

`001-nome-curto/` pode virar `002-review`, `003-critique`, etc. O importante e todo run ficar reprodutivel, com prompt, stdout e stderr separados.

**Bash:**
```bash
agy --version
codex --version
codex login status

RUN_DATE="$(date +%F)"
RUN_DIR="./_llm_runs/${RUN_DATE}/001-smoke"
mkdir -p "$RUN_DIR"
printf '%s\n' 'responda apenas OK' > "$RUN_DIR/prompt.md"
```

**PowerShell:**
```powershell
agy --version
codex --version
codex login status

$RunDate = Get-Date -Format 'yyyy-MM-dd'
$RunDir = Join-Path $PWD "_llm_runs/$RunDate/001-smoke"
New-Item -ItemType Directory -Force -Path $RunDir | Out-Null
Set-Content -Path (Join-Path $RunDir 'prompt.md') -Value 'responda apenas OK'
```

**Smoke test curto (recomendado):**
- agy: `& "<pasta-de-skills>\pipeline-multi-llm\agy-run.ps1" -PromptFile p.md -Model gemini-3.8-flash-low -OutFile out.md` (prompt "responda apenas OK"; nunca `agy` cru)
- Codex: `& "<pasta-de-skills>\pipeline-multi-llm\codex-run.ps1" -PromptFile p.md -OutFile out.md` (prompt "responda apenas OK"; nunca `codex exec` cru)

---

## GATE DE DADOS

Antes de mandar qualquer artefato pro agy ou pro Codex CLI, classificar os dados:

| Classe | Exemplos | Regra operacional |
|------|------|------|
| `PUBLICO` | docs publicas, codigo open source, textos ja publicados, requisitos sem segredo | pode delegar normalmente para T1/T2 |
| `INTERNO` | codebase privada, roadmap, draft de cliente, metricas internas, vault privado sem PII | delegar so o minimo necessario; remover segredos, tokens, PII e anexos irrelevantes |
| `RESTRITO` | PII, credenciais, chaves, contratos, segredos comerciais, financeiro sensivel, juridico privilegiado, logs com token/session | nao delegar sem mascarar ou sem aprovacao explicita; se der, resolver localmente ou com extrato anonimizado |

**Regra de gate:** se houver PII, credencial, contrato, segredo ou dado sensivel, nao delegar sem mascarar ou sem aprovacao explicita.

**Regra de duvida:** se nao tenho certeza se e `INTERNO` ou `RESTRITO`, tratar como `RESTRITO`.

---

## GATE DE LOCAL (setup Windows do autor — adapte ao seu)

**Regra fundamental de path** (validada 2026-04-30):

| Drive | Conteudo | Onde escrever notas/prompts? |
|-------|----------|------------------------------|
| `<pasta-do-drive>\obsidian\` | RAG canonico (texto/teorico) | **SIM** — toda nota nova, prompt canonico, decisao, contexto |
| `<espelho-local-do-drive>\obsidian\` | Espelho live do Drive (Google Drive Desktop sync) | **NAO** — e espelho, editar aqui confunde rastreabilidade |
| `<pasta-do-projeto>\vault-git\` | Repo Git derivado (Opcao A do council) | **SIM, mas so pra refactor pesado** — depois sync de volta pro Drive |
| `<pasta-do-projeto>\_temp\<dominio>\` | Workspace de processo (logs council, prompts intermediarios) | **SIM, mas descartavel** — output final canonico vai pro Drive |
| `<pasta-de-midia>\<cliente>\` | Producao binaria (videos, fotos, PSDs) | **NAO pra notas .md** — so binarios |

**Quando delegar a Codex/Gemini:** path destino de qualquer `.md` permanente deve comecar com `<pasta-do-drive>\obsidian\`. Workspace temporario pode ser `<pasta-do-projeto>\_temp\`. Path no espelho local do Drive e errado — confunde com espelho Drive live.

**Regra de duvida:** se o output e nota/prompt/decisao "permanente", path destino e o Drive (`<pasta-do-drive>`). Se e log de processo descartavel, `<pasta-do-projeto>\_temp\`.

---

## TIERS - quando usar cada um

| Tier | Quem | Pra que | Critica de saida |
|------|------|---------|------------------|
| 1 | agy Gemini 3.8 Flash / 3.1 Pro (slug, ver ESTADO VIVO na seção agy) | pesquisa, varredura, lookup, draft mecanico, conversao, sumario, multimodal video (vídeo = `gemini-4-argon-medium`) | leio ANTES de aceitar (criterio alto — alucina em volume) |
| 1.5 | agy Claude Sonnet 4.6 (Thinking) | tarefa AGENTIC-LEVE externa de escopo fechado que nao precisa do meu contexto de sessao (script utilitario, transformacao com logica, batch com julgamento medio) — preserva quota Max | faixas de aceitacao abaixo |
| 2 | Codex gpt-6.1-sol low (rotina) / high (pesado/critico) | code review, debug profundo, refator localizado, validacao de spec, critica final, contraponto | PAR intelectual — faixas de aceitacao abaixo, nao "criterio baixo" |
| 3 | Eu | arquitetura, voz de cliente, integracao cross-arquivo, decisao criativa, arbitragem final | sou eu mesmo |

**Regra pratica de triagem:**
- pesquisa / lookup / draft mecanico / multimodal = T1
- agentic-leve isolado sem meu contexto = T1.5 (Sonnet via agy, quota Google)
- review / refator localizado / debug profundo / validacao / critica tecnica = T2
- arquitetura / voz / integracao / decisao final = T3
- pergunta-guia: "precisa de JULGAMENTO em loop, usando as MINHAS ferramentas/contexto?" → sim = subagent Claude interno ou T3; nao = externo (T1/T1.5/T2)

## O QUE TORNA UMA TASK "SIMPLES" OU "COMPLEXA" (dimensionar o modelo ANTES de disparar)

As FAIXAS DE ACEITAÇÃO abaixo dizem quanto eu RELEIO depois; esta diz qual tier a task PEDE antes. Mesmo princípio: **capacidade de LLM não é escalar** — não existe "modelo X é 30% mais capaz". Os 3 proxies óbvios MENTEM:
- **Contexto (1M) = RAM, não CPU.** Aguentar N tokens é quanto o modelo LÊ, não quanto ENTENDE. Pré-requisito, não capacidade.
- **Parâmetros = cilindrada.** Relação com capacidade é fortemente sublinear e depende de treino/dados (100B mal treinado perde pra 10B bem treinado). Labs nem publicam mais.
- **Leaderboard = média sobre domínios que não são os do autor** (biologia, olimpíada de código). Responde pergunta que não é a nossa.

**A régua que funciona: PONTO DE RUPTURA — até onde o modelo vai sozinho antes de quebrar.** Como funcionários:
- **Haiku 4.5 / Gemini Flash / Gemma local = estagiário afiado.** Briefing mastigado + critério explícito → ótimo e barato. Vago/inferir-muito → trava ou inventa.
- **Nível júnior/pleno (Sonnet / Gemini Pro).** Aguenta multi-etapa, contorna imprevisto pequeno, "pisa no agentic". Ambiguidade/gosto ainda erra a mão.
- **Nível sênior (Opus / gpt-6-astra / Fable).** Problema aberto, replaneja no meio, gosto calibrado. Caro — não bota pra carimbar papel.

Modelo fraco não sabe MENOS FATOS — **quebra mais cedo** quando a task exige encadear decisão sem correção no meio. Não é régua contínua, é ponto de ruptura: às vezes empata com o sênior (task mastigada), às vezes despenca (inferência longa).

**Os 2 eixos que definem simples vs complexo:**

| Eixo | Pergunta | Simples (estagiário) | Complexo (sobe) |
|---|---|---|---|
| **1. Verificabilidade** | Se ele errar, eu pego barato? | saída checável (fato, schema, nº que bate com fonte) | só "parece certo", sem como conferir |
| **2. Profundidade de inferência / horizonte** | Quanto ele deduz sozinho? Quantos passos encadeia? | variáveis postas, 1-3 saltos | cruzar muita variável, inferir o não-dito, loop agentic longo, julgamento de gosto |

**A alavanca do gestor (é minha, não do modelo):** bom input REBAIXA o eixo 2. Decompor e entregar as variáveis mastigadas derruba a complexidade efetiva → o estagiário sobe de nível. MAS há uma **complexidade IRREDUTÍVEL** — o núcleo de inferência que não dá pra mastigar pra fora. Cabe abaixo do teto do modelo → bom input resolve; passa do teto → **nenhuma simplificação salva** (bati no limite do modelo, não da task). Por isso "task 2x mais simples o Gemma resolve?" = às vezes sim (baixou abaixo do teto dele), às vezes NÃO (o que sobra ainda passa).

**Default de modelo do subagente que EU disparo (Agent/Workflow) — canonizado 2026-07-17:** Sonnet/Haiku, SETADO explícito (omitir HERDA o modelo do main loop → fan-out de N Opus dreno do pool à toa). Haiku = eixo-2-baixo (extrai/classifica/lookup), Sonnet = eixo-2-médio (cruza fonte, julgamento leve). **Opus/Fable no subagente = só com OK do usuário** (regra de custo do autor). Papel no arquivo (`scout`/`worker`/`smart-worker`) já fixa o modelo. ≠ meu piso Opus do main loop, ≠ Codex/agy externo (modelo fixo lá). Porquê em [[feedback_orquestracao_multi_llm]] (2026-07-17); gatilho na cinta do CLAUDE.md.

**🔎 `pesquisador` (sonnet, read-only) — o papel que mais rende, criado 21/09.** O maior consumidor de contexto é varrer o vault: qualquer pergunta sobre decisão/projeto/histórico e, quando vou MUDAR o sistema de um cliente, o preflight (carta + DECISOES + IDENTIDADE + layout + busca no RAG). Isso vai pro `pesquisador`: ele enche o contexto DELE com os documentos e me devolve curado. Pra PRODUZIR peça ele não entra desde 30/09 (a fonte é a CARTA, skill `peca`). Base dos clientes: `<pasta-do-drive>/obsidian/<pasta-do-cliente>/`.

🔴 **O contrato inegociável dele: CITAÇÃO LITERAL + caminho + data, nunca paráfrase.** No vault "o exemplo É a regra" ([[feedback_brand_profile_e_espelho]], nota não incluída) — um resumo tipo *"tom acolhedor, cores da marca"* produz **peça errada com contexto limpo**, que é pior que peça errada com contexto sujo: eu não percebo que perdi a regra. Síntese só pra navegação ("há 4 entradas sobre CTA, as 2 vigentes são estas"), nunca no lugar do texto da regra. Se não der pra saber o que é vigente vs revogado, ele traz os dois marcados. E **não mistura cliente**: "a ótica" sem nome volta como `CLIENTE AMBÍGUO`, nunca escolhido por ele.

⚠️ **Teste de suficiência:** se depois do relatório dele eu precisar abrir o `DECISOES.md` mesmo assim, a delegação falhou — o retorno tem que ser generoso no que decide a peça (regra literal, hex, CTA, proibição) e seco no resto.

**👷 PAPEL vem antes do modelo — canonizado 2026-09-21.** Subagente interno agora tem papel nomeado em `~/.claude/agents/`, e o modelo mora no ARQUIVO (não na chamada): **`scout`** (haiku, read-only) acha/extrai/confere e devolve fato+caminho · **`worker`** (sonnet) executa escopo FECHADO já decidido · **`smart-worker`** (sonnet, effort alto) pega ambíguo/difícil e é obrigado a medir no alvo real. Chamar pelo papel (`subagent_type`) em vez de `general-purpose` + prompt solto: o modelo certo entra sozinho e o erro de "omiti o model e rodou Opus" deixa de ser possível. ⚠️ **Papel criado agora só fica chamável na sessão SEGUINTE** (o registro carrega no boot) — na sessão corrente, usar `Explore`/`general-purpose` com `model` explícito.

**🔴 Gatilho de delegação = DOIS eixos, basta um (refinado 2026-09-21 pelo autor).**

- **Eixo 1 — tarefa FECHADA:** o brief cabe em 5 linhas E o resultado se confere sem o contexto da sessão → delega, **mesmo sendo 1 item**. (Não é "N≥5 delega, N<5 faço" — contagem nunca foi o critério.)
- **Eixo 2 — tarefa SUJA:** o julgamento é MEU, mas a execução cospe ruído que eu leria e jogaria fora — stack trace, log de teste, 800 linhas de `.tsx`, saída de varredura. Aqui **não delego a tarefa, delego a EXECUÇÃO**: fico com contrato, direção e veredito; o executor come o ruído e devolve destilado. Analogia do autor: *"se eu boto meu melhor jogador pra marcar a zaga a partida toda, quando a bola chegar no pé dele ele está morto de fadiga"* — o Opus dele é comprado por **habilidade**, não por capacidade de digitar código; chegar limpo na hora de julgar "ficou fluido?" vale mais que ter visto a variável quebrar.

**Contrato de papéis — vale pra QUALQUER produção, não só vídeo:** **diretor** (Claude: contrato, direção, arbitragem) · **executor** (agente interno / agy / NIM) · **olho** (lente que mede ou assiste; nunca o próprio executor) · **régua** (o usuário).

⚠️ **Delegação teatral — o furo que anula o ganho:** delegar arquivo que EU escrevi ou li **não tira ele do meu contexto** (medido 16/09): o harness injeta o diff truncado no meu turno seguinte. Então entrego por **BRIEF** (caminho + especificação), **não escrevo stub** que o executor vai reescrever, e **não dou `Read`** no arquivo que ele está editando — confiro por render, medição ou relatório. Ver [[feedback_custo_mora_no_contexto]].

⚠️ **Limite honesto do eixo 2:** executor sem contrato NUMÉRICO vira telefone sem fio — ele conserta a variável sem saber que o alvo era fluidez, e eu julgo fluidez sem saber da variável. Delegar execução exige **alvo mensurável** (easing, duração, overshoot, tamanho, tempo) + **instrumento** que meça. Sem isso, não é delegação: é terceirizar o erro.

- **Eixo 3 — VARRIDA (limiar Anthropic, blog "Subagents in Claude Code"):** *"When a task requires exploring ten or more files, or involves three or more independent pieces of work, that's a strong signal to direct Claude toward subagents."* Conta EXPLORAÇÃO e INDEPENDÊNCIA, não itens.

⛔ **Anti-padrões — os dois fornecedores batem na mesma tecla (lido 21/09/2026):**

| não delegue | por quê |
|---|---|
| passo sequencial cujo seguinte precisa do output INTEIRO do anterior | o destilado não basta; o handoff perde o que importa |
| escrita paralela no MESMO arquivo | conflito + overhead de coordenação (ambos os docs citam) |
| tarefa pequena e rápida | o overhead (~25k de piso) engole o ganho |
| criar muitos especialistas | Anthropic: *"too many specialist agents"* afoga a triagem automática |

🎯 **"Narrow and opinionated" — mas no BRIEF, não em mais agente (Codex docs).** Agente estreito performa melhor porque gasta menos banda DELE descobrindo o que fazer. Como o registro de papéis não pode inchar, a estreiteza entra pelo briefing: **contrato + alvo mensurável + formato de saída, e NADA da história da sessão**. Brief de 5 linhas focado > 3 páginas de contexto: protege a banda dele e melhora o output. Especialista com arquivo próprio só quando a tarefa for recorrente e comprovada (mesmo padrão e mesmo instrumento em várias sessões).

🧭 **Herança de config é igual nos dois:** valor explícito no spawn → default do papel → valor do pai. É por isso que omitir `model` herda o Opus do main loop — não é bug do Claude Code, é o padrão da categoria.

📌 **Onde a vivência do autor ganha da doc:** os docs pesam token como DINHEIRO; no plano dele o gargalo é **COTA** (~75× o valor pago, ver [[feedback_custo_mora_no_contexto]]) — então "consome mais tokens" não é argumento contra delegar ruído, e é argumento forte contra delegar micro. E nada nos docs cobre **crítico nunca ser outro Claude** nem a **delegação teatral** (diff reinjetado): as duas são medição local.

📏 **A RÉGUA É AMORTIZADA, não bruta.** O gasto bruto do subagente assusta e engana, mas se EU ler o corpus, ele é relido a cada turno. (Medição do run e o que o Codex derrubou da v1: `ref/historico-2026.md`.)

> **`T > custo_do_agente ÷ (corpus − relatório)`**
> **Triagem: vou usar esse contexto por mais de ~4 turnos?** Sim → delega.
> ⚠️ O denominador desconta o relatório — ele também fica no meu contexto.

**Os 2 guarda-corpos:**
1. 🔴 **A contagem de turnos governa contexto ESTÁVEL; NÃO veta o eixo SUJA.** Stack trace de 900 linhas pra uma decisão única responde "não uso por 4 turnos" — e delegar continua certo, porque ali o dano é poluição AGORA, não turno futuro. **Conflitou, SUJA vence.**
2. 🔴 **Piso de overhead vem ANTES dos gatilhos.** Custo do subagente ≈ **21,5k de piso + ~1,7k por tool call** (⏳ medido 21/09; regressão de 2 pontos, hipótese). Trabalho inteiro que caiba abaixo do piso e não seja sujo = **faço eu**, mesmo passando em FECHADA ou em "10+ arquivos" (contraexemplos: trocar 1 chave num JSON de 20 linhas; 12 arquivos minúsculos somando 4k).

⚠️ **Brief largo infla o agente:** pedir "o que já existe pra reaproveitar" em aberto traz arquivos que não decidem nada — brief estreito, sempre.
📉 **Relatório enxuto vale mais do que parece:** cada 1k devolvido pelo agente vira 30k numa sessão de 30 turnos — literal no que decide, ponteiro no resto.

**E subagente Claude NÃO é barato:** sai do mesmo pool Max 5x. Volume mecânico desce primeiro pra cota externa (agy/NIM); subagente meu se justifica quando o ganho é **proteger a banda do main loop** — que é onde mora o custo — ou rodar frentes em paralelo de verdade.

**Modelos LOCAIS no zoo (3060, [[reference_localai_headless]], nota não incluída) — perfil de quebra MEDIDO:** o Gemma 12b local NÃO é "Gemini com −X%", o SHAPE de quebra é outro. Bom em copy PT-BR (Gemma 4 bateu Qwen 9B), **ZERO agêntico** (r=0,012 FC×agêntico; zera em web search → NUNCA dar loop de ferramenta a ele), fraco em raciocínio multi-passo (heretic pior: −7,8pp GSM8K). Rota: local só pra geração de texto/copy de escopo fechado, input mastigado, SEM ferramenta; pesquisa/agêntico/cruzamento continua Claude/Codex/Gemini. `localai ask` roda; não setar `--max-tokens` curto (Gemma QAT raciocina antes → content vazio).

## FAIXAS DE ACEITACAO (quando eu releio o output do executor)

A regua NAO e inteligencia do executor — e **verificabilidade + raio de explosao**:

| Faixa | Quando | Minha acao |
|------|------|------|
| **Confia no verde** | verificacao MECANICA embutida: teste/lint/build/schema/render passa = aceito. Quem confere e a maquina | zero releitura; só checo o verde + exit code |
| **Spot-check** | escopo fechado (1 arquivo/componente, spec clara), executor entrega diff + evidencia de teste | leio veredicto + 1-2 passagens-chave |
| **Gate T3 obrigatorio** | integracao cross-arquivo em estado que EU seguro, voz de cliente/marca, escrita em canonico (Drive/vault/memoria), dado RESTRITO, publicacao/destrutivo | releitura integral + arbitragem minha |

Racional: supervisao cobre assimetria de CONTEXTO (regras de cliente, historico, estado da sessao), nao de inteligencia. GPT 5.5 high com spec completa + verificador mecanico = autonomia total ("so preciso do ok dele"). Saida Gemini continua criterio alto SEMPRE (alucina com confianca em volume).

**Se a tarefa for mista:**
- descoberta factual -> T1
- implementacao, review ou validacao localizada -> T2
- sintese, arbitragem e decisao final -> T3

Nao mandar tarefa mista longa para um unico tier por conveniencia.

**Critico final:** Codex (T2). Peca de cliente: UM revisor Codex as cegas (`peca\ref\REVISOR.md`).

**REGRA DURA — critico nunca e outro Claude.** Com Sonnet/Opus 4.6 "gratis" no agy, a tentacao e mandar TUDO pro Claude barato — inclusive critica. NAO: executor pode ser qualquer um (Sonnet via agy inclusive); **critico/contraponto vem de OUTRA linhagem** (Codex; o Gemini fica com volume e midia). O ouro do contraponto e o caminho logico diferente de um harness/modelo diferente — Claude criticando Claude e groupthink com etapas. Unica excecao: motion por codigo (`motion-studio`), revisor = Claude Opus novo, as cegas, contra um viral.

## Como BRIEFAR o critico (autor, 2026-07-29) — postura construtiva, nao poda

> Principio geral, pra critico de artefato tecnico. **Peca de cliente: `<pasta-de-skills>\peca\ref\REVISOR.md`** (UM revisor as cegas, zero contexto; nao e a escada de 2 passadas abaixo).

Diagnostico do autor: *"o critico so tem valor quando a critica vem pra melhorar, e nao pra podar toda e qualquer ideia."* A postura destrutiva **e induzida pelo briefing** ("procure o que nao pode ir ao ar, nao elogie, liste bloqueios") + assimetria de incentivo (o critico nao paga preco por matar boa ideia, so por deixar passar erro) + falta de historico (por isso "descobre" o que ja estava resolvido no vault ha 2 dias).

**DUAS PASSAGENS, papeis separados** — juntos, o verificador engole o editor:

| Passagem | Papel | Cota | Saida |
|---|---|---|---|
| 1 | **Verificador factual** — e falso, ilegal, ou e promessa? | **SEM cota** (erro factual nao pode sumir por limite) | agrupado, com evidencia + correcao concreta: corrigir, qualificar ou remover |
| 2 | **Editor** — esta fraco, generico, esquecivel? Recebe a peca **ja saneada** | so as intervencoes que mudam a peca/o veredicto | cada uma no formato `o que preservar -> problema -> substituicao proposta` |

**Regras duras do briefing:**
- **Nenhum bloqueio vale sem alternativa concreta** que preserve a intencao original. "Nao pode X" sem substituto **e descartado na entrada**.
- **Exigir que diga o que esta BOM e nao pode ser perdido** — nao por elogio: quem conserta sem saber o que preservar entrega peca aguada.
- **"Esta bom, pode seguir" e resposta legitima** — dizer isso explicitamente no briefing, senao ele inventa problema pra justificar a existencia.
- **O veredito e MEU, item a item.** Aplico o que e falso/ilegal/promessa; **recuso o que so tira alma** e registro a recusa com motivo. Ver [[feedback_regua_nao_mentir_glamurizar]] (nota não incluída).

**Terceira voz** (variante): quando o objetivo e melhorar um desenho e nao validar uma peca, briefar como *"voce e a terceira voz numa conversa entre dois que ja concordam — ache o que nao estamos vendo"*, com as mesmas regras (cota, alternativa obrigatoria, "esta bom" permitido). Validado em 29/07 no desenho da politica de rotas: rendeu 4 melhorias estruturais, zero poda.

**Regras de integridade:**
- gerador nao aprova a propria saida
- para mudanca de codigo, critica final sem teste executado = tarefa incompleta
- se Gemini e Codex divergirem em recomendacao material, eu nao escolho por maioria; identifico a divergencia, estreito o prompt e arbitro em Tier 3 com racional explicito
- se a divergencia for factual, gero uma rodada adicional T1/T2 so para isolar o dado faltante

---

## WORKFLOW (Claude paralelo) vs pipeline-multi-llm

Esta skill cobre **escada sequencial multi-LLM** (vozes diferentes em serie). Existe topologia complementar: **Workflow tool** (Anthropic, research preview mai/26) faz fan-out de N subagents Claude em paralelo. Nao sao concorrentes — sao eixos diferentes.

### Diferenca em uma frase

- **pipeline-multi-llm** = vozes DIFERENTES em sequencia (T1 Gemini -> T2 Codex -> T3 eu, qualitativo)
- **Workflow** = MESMA voz Claude em paralelo (ate ~16 simultaneos, 1000 total na run, quantitativo)

### Decisao de qual usar

| Sinal da tarefa | Ferramenta |
|---|---|
| "sera que estou errado?" / contraponto / arbitragem | pipeline (Codex pega vies meu) |
| Code review profundo de algo critico | pipeline (Codex T2) |
| Decisao arquitetural unica | T3 (eu) |
| Groupthink mata o resultado | pipeline (modelos diferentes) |
| Quota Claude e gargalo | pipeline (drena Codex/Gemini) |
| Auditar/migrar/processar itens independentes (tarefa FECHADA/VARRIDA) | Workflow |
| Find -> verify -> synth em escala | Workflow |
| Mass generation com QA cruzado | Workflow |
| Fricao de setup importa, tarefa nao critica | Workflow |

### Regra de bolso

Fan-out paralelo (Workflow) quando os itens sao independentes e a tarefa e FECHADA/VARRIDA, com `model` setado no agente. Contraponto/arbitragem → escada. Itens independentes + critica externa → hibrido (Workflow + Codex no agregado).

### Hybrid: combinar os dois

Cada agente Workflow pode chamar Codex/Gemini via Bash internamente. Escala + diversidade num pacote so:

```text
Workflow: audit 50 roteiros do canal
  - 16 agentes paralelos Claude (cada um le 1 roteiro, aplica voz do canal)
  - Cada agente: Bash -> codex-run.ps1 (critica adversarial por roteiro)
  - Agente synth: integra 50 outputs + Codex final no agregado
```

### Quando NAO usar Workflow

- Tarefa tao pequena que o setup sai mais caro que fazer (1 item fechado = um subagente so, sem Workflow)
- Decisao criativa unica (voz cliente, arbitragem)
- Contexto cross-arquivo que nao decompoe em items independentes
- Groupthink fatal sem critico externo (16 Claudes pensam igual)
- Material RESTRITO ainda nao mascarado (subagents tambem veem)

### Quotas

- **Workflow** drena o MESMO pool Claude do main loop (eu) — N agentes paralelos queimam tokens rapido
- **pipeline** drena 3 planos separados (Claude + ChatGPT/Codex + Google AI Pro) — mais robusto em quota total, custa mais setup

Com a cota Claude apertada: se a tarefa cabe em Gemini Flash + Codex T2, pipeline e mais sustentavel. Workflow vale quando paralelismo > diversidade de vies, ou quando ja drenei Codex/Gemini.

### Como ativar Workflow

- Mencionar "workflow" ou "workflows" na mensagem (opt-in automatico)
- Pedir "orquestra com subagents em paralelo" / "fan out N agentes"
- `/workflows` ve progresso ao vivo da arvore de agentes
- Sintaxe e docs do tool ja vem injetada quando Workflow ativa — nao preciso decorar

### Gate de dados / local continuam valendo

Workflow nao bypassa nada:
- Gate de dados (PUBLICO/INTERNO/RESTRITO) aplica a cada agente
- Gate de local (Drive pra notas, pasta temp pra processo) aplica a outputs
- Worktree isolation (`isolation: 'worktree'`) so se agentes mutarem arquivos em paralelo no mesmo repo git (caro: ~200-500ms setup por agente)

---

## ANTIGRAVITY CLI (agy) - referencia operacional ⭐ T1/T1.5

> 🔴 **ESTADO VIVO (slugs conferidos em 30/09/2026 via `agy models`).** Binário em `%LOCALAPPDATA%\agy\bin\agy.exe` (versão: `agy --version`); o `agy-run.ps1` **v2** usa `--output-format json` e recebe **SLUG**, não label: `gemini-3.8-flash-{low|medium|high}`, `gemini-3.7-flash-*`, `gemini-3.6-flash-*`, `gemini-3.1-pro-{low|high}`, `claude-sonnet-4-6`, `claude-opus-4-6-thinking`, `gpt-oss-120b-medium` (set ao vivo: `agy models`). **O effort mora no SLUG**: trabalho mecânico = `gemini-3.8-flash-low` (o `-medium` queima ~18× mais tempo pensando). `-Effort` só se BATER com o slug — diferente dá `invalid model selection … conflicts with --effort` e nada roda (28/09: mandei `-medium -Effort low` nos 3 jobs, os 3 morreram em 0 s). Pro e GPT-OSS idem: slug sem `-Effort`. `agy-extract.py`/`agy-inspect.py` estão obsoletos.
> 🔴 **`-Cd` / `-PromptFile` / `-OutFile` sempre com o path LONGO (`<pasta-do-usuario>\...`; o caso é nome de usuário do Windows com espaço).** O harness entrega o scratchpad em 8.3 (`C:\Users\<NOME~1>\...`) e os dois wrappers (agy e Codex) morrem em `Push-Location` com esse path. E fixar o console em UTF-8 antes de chamar, senão o acento volta destroçado.
> Detalhe e medições: memória `reference_antigravity` (§2026-09-02) e `reference_gemini_codex_cli`.

agy em `%LOCALAPPDATA%\agy\bin\agy.exe`, login OAuth via keyring.

### Modelos (quota Google AI Pro do autor, ociosa)

| Slug (`-Model` do `agy-run.ps1`) | Quando |
|------|------|
| `gemini-4-argon-medium` | **OLHO DE VÍDEO PADRÃO** (OK do autor 16/10; pool semanal próprio, vídeo de 16 min ≈ 5% da semana) — ver "multimodal video" na triagem |
| `gemini-3.8-flash-{low,medium,high}` | T1 volume: varredura, draft, conversao, decupagem; fallback de vídeo se o pool do Argon acabar |
| `gemini-3.1-pro-{low,high}` | T1 pesado: research profunda, analise critica de midia |
| `claude-sonnet-4-6` | T1.5: agentic-leve externo, executor com julgamento medio |
| `claude-opus-4-6-thinking` | T1.5 pesado (raro — so quando Sonnet nao da conta e nao vale Max) |
| `gpt-oss-120b-medium` | **3ª linhagem (NÃO-Claude) → crítico/contraponto BACKUP** quando Codex (cota/limite) e Gemini estiverem ocupados; council. Par técnico de outra linhagem. |

Slug = 1a coluna do `agy models` (lista o set atual; muda com rollout). **Pool de CRÍTICO/contraponto (outra linhagem) = Codex + Gemini + GPT-OSS 120B** — se Codex bater limite de cota, cair pro Gemini OU GPT-OSS, nunca pra Claude. Lembrete: **Claude (Sonnet/Opus) via agy = EXECUTOR barato na cota Google** (mesma linhagem que eu → nunca crítico); GPT-OSS/Gemini/Codex = crítico.

### Flags chave (validadas no --help 1.0.7)

| Flag | Descricao |
|------|-----------|
| `-p / --print "prompt"` | headless: roda 1 prompt e imprime resposta |
| `--model <slug>` | modelo da sessao (slug do `agy models`) |
| `--print-timeout` | timeout do modo print (default 5m) |
| `--dangerously-skip-permissions` | auto-aprova tool calls (equivalente do yolo) |
| `--add-dir` | adiciona dir ao workspace (repetivel) |
| `--sandbox` | restricoes de terminal |
| `-c / --continue` | continua ultima conversa |
| `--conversation ID` | retoma conversa por ID |
| Subcomandos | `models`, `update`, `plugin`, `install`, `changelog` |

### Padrao de invocacao CANONICO no harness (VALIDADO 2026-06-11) ⭐

**Invocacao:** `agy-run.ps1` (nesta pasta; v2, JSON nativo); cwd limpa; `-Model <slug>` sem `-Effort`.
```powershell
Set-Location '<pasta-do-projeto>\_temp\work-dir-limpo'   # cwd limpa, nunca raiz de drive
# T1 volume/pesquisa:
& "<pasta-de-skills>\pipeline-multi-llm\agy-run.ps1" -PromptFile prompt.md -Model gemini-3.8-flash-low -OutFile out.md   # v2: SLUG (o effort vem nele), path LONGO
# T1.5 agentic-leve (Claude na quota Google):
& "<pasta-de-skills>\pipeline-multi-llm\agy-run.ps1" -PromptFile prompt.md -Model claude-sonnet-4-6 -OutFile out.md
```

- Num terminal REAL do usuario, `agy -p "..."` imprime normal — o wrapper e pro harness.
- Cold start ~5-20s (language server Go); `--print-timeout` default 5m. Tarefa >60s → `run_in_background: true`.
- Processos `agy` pendurados de chamadas erradas viram zumbis — `Get-Process agy | Stop-Process -Force` limpa.
- `trustedWorkspaces` no settings.json controla onde opera sem prompt de confianca; `--add-dir` pra dir extra pontual.
- Video/audio/imagem inteiros → agy (detalhe na nota de memoria `reference_antigravity`). A receita antiga era do Gemini CLI e morreu com ele.

---

## CODEX CLI - referencia operacional

### ⭐ CAMINHO CANONICO no Windows: `codex-run.ps1` + CODEX_HOME dedicado

**No harness (Windows/pwsh), SEMPRE invocar via `codex-run.ps1` (nesta pasta)** — igual `agy-run.ps1` faz pro agy. Ele embute os 3 fixes (config isolado, sandbox bypass, prompt UTF-8 via stdin) e retorna a resposta limpa em `out.md` (ruido do Codex vai pro `.err.log`).

```powershell
# rotina (gpt-6.1-sol / effort low — rapido e barato):
& "<pasta-de-skills>\pipeline-multi-llm\codex-run.ps1" -PromptFile prompt.md -Cd '<pasta-do-projeto>\_temp\work' -OutFile out.md
# critica / debug pesado (gpt-6.1-sol / effort high):
& "<pasta-de-skills>\pipeline-multi-llm\codex-run.ps1" -PromptFile crit.md -Effort high -OutFile out.md
```

**SETUP DO CODEX_HOME (1x por maquina — feito na maquina do autor em 2026-07-06):**
Existe `~/.codex-cli/` com `auth.json` (copia do `~/.codex/`) e um `config.toml` MINIMO (so `model`, `model_reasoning_effort`, `service_tier="fast"`, `approval_policy="never"`, `sandbox_mode="workspace-write"`, `[memories] use_memories=false`). O wrapper aponta `CODEX_HOME` pra la e re-sincroniza o `auth.json` sozinho quando o app desktop refresha o token. **Por que separado:** o `~/.codex/config.toml` e do APP DESKTOP (service_tier=priority, gpt-5.5/xhigh, dezenas de plugins/projects) e quebra a CLI — nunca editar aquele; a CLI vive no `~/.codex-cli`, isolada. Recriar o setup se a pasta sumir: `New-Item -ItemType Directory ~/.codex-cli`; `Copy-Item ~/.codex/auth.json ~/.codex-cli/`; escrever o `config.toml` minimo.

### Modelos

| Modelo | Quando usar |
|--------|-------------|
| `gpt-6.1-sol` | **default do wrapper** ("latest workhorse") — rotina com `-Effort low`; critica, debug e contraponto com `-Effort high` |
| `gpt-6-astra` | o mais forte ("frontier") — auditoria de alto risco/baixo volume, quando o sol high nao basta |
| `gpt-6-sol` / `gpt-6-luna` | geracao anterior / rapido e barato pra tarefa facil |
| `gpt-5.6-sol/terra/luna` | "older", ainda listados; sem motivo pra usar |

Lista conferida em 20/10/2026 (CLI 0.163.0, conta ChatGPT): `gpt-6.1-sol` e `gpt-6-astra` passaram no smoke. `gpt-5.5`/`gpt-5.4` dao 400. Efforts: low/medium/high/xhigh/max (+`ultra` = delega tarefa sozinho; luna nao tem). **Modelo novo so aparece com CLI nova** → `npm i -g` do pacote openai/codex (pacote npm com escopo openai, versão latest) e conferir com `codex debug models` (com `CODEX_HOME=~/.codex-cli`). ⏳ reconferir a partir de dez/2026. Tratar a saida pelas FAIXAS DE ACEITACAO (par intelectual), nao reler tudo por reflexo.

### Subcomandos chave

| Comando | Uso |
|---------|-----|
| `codex exec "task"` | nao-interativo - delegacao padrao |
| `codex exec resume --last "follow"` | continua sessao anterior |
| `codex login` / `codex login status` | OAuth ChatGPT ou `--with-api-key` |
| `codex mcp add/list/remove` | gerenciar MCP servers |
| `codex sandbox -- cmd` | rodar comando arbitrario com sandbox |
| `codex apply TASK_ID` | aplica diff de tarefa cloud |
| `codex e` | alias pra `codex exec` |

### Flags chave (codex exec)

| Flag | Descricao |
|------|-----------|
| `-m / --model` | slug vivo (tabela Modelos acima) |
| `--full-auto` | atalho workspace-write + on-request — ⚠️ **NAO funciona no Windows** (cai read-only, nega subprocesso) |
| `--dangerously-bypass-approvals-and-sandbox` | **UNICO modo que executa no Windows** (= danger-full-access); embutido no `codex-run.ps1` |
| `--sandbox` | `read-only` / `workspace-write` / `danger-full-access` — no Windows so `danger-full-access` (via bypass) roda de fato |
| `-a` (flag de aprovacao; forma curta) | `untrusted` / `on-request` / `never` |
| `--json` | JSONL stream em stdout |
| `-o / --output-last-message` | salva mensagem final em arquivo |
| `--output-schema` | valida output contra JSON Schema |
| `--ephemeral` | sem disk persistence |
| `--skip-git-repo-check` | permite fora de repo git (essencial pra vault Obsidian, /d/_temp) |
| `-i / --image` | anexa imagens |
| `--search` | habilita web search |
| `--add-dir` | concede write a dir adicional |
| `-c key=value` | override de config inline |
| `-C / --cd` | working dir |

### Padroes de invocacao

**Code review / segunda opiniao (Tier 2 default):**
```powershell
& "<pasta-de-skills>\pipeline-multi-llm\codex-run.ps1" -PromptFile prompt.md -Cd '<pasta-do-projeto>\_temp\work' -OutFile out.md
```

**Stdin pipe:**
```bash
npm test 2>&1 | codex exec "resuma falhas e proponha o menor fix" | tee summary.md
cat error.log | codex exec -   # quando stdin e o prompt completo
```

**Output estruturado:**
```bash
codex exec --json --skip-git-repo-check "extrair metadata" \
  --output-schema schema.json -o metadata.json
```

### Erros comuns + fix

**`--full-auto` falha no Windows** - Windows Sandbox + bubblewrap ausente quebra full-auto em alguns casos. Fallback: `--dangerously-bypass-approvals-and-sandbox` (uso liberado em ambiente local controlado do autor).

**`requires git repo`** - usar `--skip-git-repo-check` quando rodando em pasta nao-git (vault Obsidian, /d/_temp, etc).

**Auth expirou** - `codex login status` pra checar; `codex login` pra OAuth de novo.

**MCP `required = true` falha** - exec sai com erro. Checar `codex mcp list`.

**`CODEX_API_KEY` nao reconhecida** - so suportada em `codex exec`. Outros comandos usam OAuth.

---

## TEMPLATES DE PROMPT PRONTOS

> **Invocacao agy nos templates abaixo:** **no harness do Claude Code (Windows), SEMPRE via `agy-run.ps1`** (secao AGY acima) — `agy` cru nao se usa.

### T1 - Pesquisa profunda em Gemini Pro

Usar quando eu preciso descobrir, comparar e recomendar com base em fontes externas ou corpus grande.

```text
Voce e pesquisador especialista em [TEMA].

PARAMETROS:
- objetivo de decisao: [o que precisa ser decidido no fim]
- profundidade: [curta | media | longa]
- formato de saida: [memo-operacional | tabela-decisao | plano-de-execucao | checklist]
- idioma: PT-BR

REGRAS:
- usar a MENOR profundidade que resolva o problema
- citar fontes com URL e data
- separar fato observado de interpretacao
- se faltar dado, dizer exatamente o que falta

CONTEXTO:
- [o que o usuario esta fazendo]
- [stack disponivel]
- [restricoes reais]

PESQUISAR:
1. [topico 1]
2. [topico 2]
3. [riscos, anti-padroes, cemiterio]

ENTREGA OBRIGATORIA:
1. resumo executivo
2. achados factuais por prioridade
3. recomendacao concreta com trade-offs
4. proximos passos operacionais
5. fontes

AJUSTE DE PROFUNDIDADE:
- curta: responder em formato util de decisao rapida, sem inflar
- media: detalhar criterios, opcoes e trade-offs
- longa: aprofundar, comparar alternativas e mapear riscos em mais detalhe
```

Invocar:
```powershell
Set-Location '<pasta-do-projeto>\caminho\work-dir-limpo'   # cwd limpa, nunca raiz de drive
& "<pasta-de-skills>\pipeline-multi-llm\agy-run.ps1" -PromptFile prompt.md -Model gemini-3.1-pro-high -OutFile out.md
```

### T1 - Varredura / diagnostico em Gemini Flash

Usar quando eu preciso inventariar, medir, ranquear e achar padroes em pasta grande, vault ou codebase.

```text
Voce tem file access local em $PWD.

PARAMETROS:
- objetivo: [diagnostico de Y]
- foco: [estrutura | cobertura | links | smells | metricas]
- profundidade: [curta | media | longa]
- formato de saida: [relatorio-operacional | inventario | plano-p0-p2]
- idioma: PT-BR

REGRAS:
- usar Python pra contagens quando for mais confiavel que regex shell
- listar apenas acoes materialmente diferentes
- separar evidencia de recomendacao

ANALISAR:
1. estatistica: count, distribuicao, top N
2. estrutura: orfas, hubs, links quebrados, ramificacoes perdidas
3. saude: itens nao modificados ha >X dias, granularidade ruim
4. pontos de ramificacao perdidos: conceitos mencionados N+ vezes que nao viraram link/tag/nota

ENTREGA OBRIGATORIA:
1. resumo executivo
2. evidencias principais
3. tabela priorizada P0/P1/P2
4. acoes concretas com:
   - acao
   - evidencia
   - impacto
   - esforco S/M/L
   - executor recomendado (humano / Claude / Gemini / Codex / automatizado)

AJUSTE DE PROFUNDIDADE:
- curta: so os maiores problemas e o menor plano de correcao
- media: mapa util da area + prioridades claras
- longa: inventario mais amplo + backlog operacional enxuto
```

Invocar:
```powershell
Set-Location '<pasta-do-projeto>\caminho\codebase'   # cwd vira workspace; nunca raiz de drive
& "<pasta-de-skills>\pipeline-multi-llm\agy-run.ps1" -PromptFile prompt.md -Model gemini-3.8-flash-low -OutFile out.md
```

### T2 - Critica final em Codex (alto impacto)

Critica de peca de cliente: `<pasta-de-skills>\peca\ref\REVISOR.md`. Esta invocacao e pra artefato tecnico.

Usar quando eu preciso de uma segunda opiniao tecnica forte antes de aprovar ou integrar.

Invocar:
```powershell
& "<pasta-de-skills>\pipeline-multi-llm\codex-run.ps1" -PromptFile prompt.md -Effort high -OutFile out.md
# (critica de alto impacto = gpt-6.1-sol high; rotina = gpt-6.1-sol low; o mais forte = gpt-6-astra)
```

### T2 - Code review em Codex

Usar quando eu preciso revisar codigo com formato operacional e sem cosmetica.

```text
Revise $PWD/path/to/file ou diff anexado.

FOCO:
1. bugs ou edge cases nao cobertos
2. anti-padroes ou code smells relevantes
3. security issues (OWASP Top 10 quando aplicavel)
4. performance issues evidentes
5. veredicto final: MERGE / REVISAR / REFATORAR

FORMATO OBRIGATORIO DE CADA ACHADO:
[SEV1|SEV2|SEV3] arquivo:linha - problema / impacto / menor correcao / veredicto

REGRAS:
- listar so os achados que mudam o veredicto
- pular sugestao cosmetica
- se nao houver linha precisa, usar arquivo:sem-linha
- se nao houver achados materiais, dizer explicitamente "sem achados materiais"
```

### T2 - Arbitrar divergencia Gemini vs Codex

Usar quando Gemini e Codex discordam em recomendacao material. Este template organiza a divergencia em T2; a decisao final continua em T3.

```text
Voce vai comparar dois artefatos:
- GEMINI.md
- CODEX.md

OBJETIVO:
- identificar concordancias reais
- separar divergencia factual de divergencia de criterio
- apontar qual lado esta melhor sustentado
- dizer o dado faltante que resolveria o empate

ENTREGA OBRIGATORIA:
1. concordancias
2. divergencias reais
3. quem vence e por que
4. dado faltante para desempatar

REGRAS:
- nao force consenso
- nao decida por "maioria"
- se evidencia estiver insuficiente, escreva literalmente: precisa arbitro T3
```

---

## RECEITAS COMPLETAS

### R1: Pesquisa estado da arte + critica
1. Gemini Pro pesquisa profunda -> `output-research.md`
2. Codex critica research -> `output-critique.md`
3. Eu integro em recomendacao final
4. (opcional) Gemini gera versao curta executiva

### R2: Varredura grande de codebase + plano
1. Gemini Flash varre dir -> `output-vault.md` (relatorio + lista priorizada P0/P1/P2)
2. Eu sintetizo proposta arquitetural (Tier 3, sem delegar)
3. Codex valida proposta -> `validation.md`
4. Apresento plano final pro usuario aprovar

### R5: Bugfix / code change
1. triagem: reproduzir erro, coletar log, classificar dado (`PUBLICO` / `INTERNO` / `RESTRITO`) e decidir o tier por parte da tarefa
2. implementacao: fazer o menor diff localizado que resolve a causa raiz
3. testes: rodar teste, lint, comando de reproducao ou smoke test minimo; se nao rodar nada, marcar como incompleto
4. critica externa: para codigo, preferir Codex T2; para contexto leve, Gemini T1 pode fazer critica auxiliar
5. integracao: eu arbitro feedback, ajusto a solucao final e fecho com veredicto claro

---

## CHECKLIST OPERACIONAL DO GESTOR

Antes de DELEGAR, eu (Claude) checo:

- [ ] Classifiquei os dados? (`PUBLICO` / `INTERNO` / `RESTRITO`)
- [ ] Triagem de tier feita? (`T1 = pesquisa/draft`, `T2 = review/validacao`, `T3 = integracao/decisao`)
- [ ] Se a tarefa for mista, decomposicao T1 -> T2 -> T3 esta explicita?
- [ ] CWD da CLI delegada esta LIMPO? (nao raiz de drive, nunca `C:\` ou equivalente)
- [ ] Pasta de run criada em `./_llm_runs/YYYY-MM-DD/`?
- [ ] Modelo certo pro tier? (agy: `-Model <slug>` do `agy models`; Codex: `gpt-6.1-sol` low rotina / high pesado)
- [ ] Headless setado? (agy: `agy-run.ps1`; Codex: `codex-run.ps1`, que ja embute o bypass de sandbox)
- [ ] Output redirecionado pra arquivo? (`out.md` e `err.log`)
- [ ] Timeout adequado? (`600000` = 10min pra pesquisa profunda)
- [ ] `run_in_background: true` se tarefa > 60s?
- [ ] Prompt salvo em arquivo pra evitar escape hell em aspas?
- [ ] Codex tem `--skip-git-repo-check` quando fora de repo git?
- [ ] Critico final engatilhado no fim (escada Gemini/Codex)?

Antes de ACEITAR output:

- [ ] Saida Gemini lida ANTES de aceitar (criterio alto - pode alucinar com confianca)?
- [ ] Saida Codex/Sonnet-agy tratada pela FAIXA DE ACEITACAO certa? (verde mecanico = confio; spot-check = veredicto + passagens; gate T3 = releitura integral)
- [ ] `stderr` foi checado, nao so `stdout`?
- [ ] Exit code foi `0`?
- [ ] Se Gemini e Codex divergiram, arbitragem T3 foi feita com racional explicito?

---

## ECONOMIA DE TOKENS - regra do gestor

Meu (Claude) token e o caro — a conta NAO e centavos por chamada, e PRESSAO DE QUOTA num teto duro (cota Claude apertada, dividida com o sócio) vs cota Google/OpenAI ociosa. Quando posso delegar:
- pesquisa web -> agy (Gemini Pro/Flash)
- varredura de codebase/vault -> agy (Gemini Flash)
- agentic-leve de escopo fechado sem meu contexto -> agy (Claude Sonnet 4.6)
- critica de output meu -> Codex (peca complexa) ou Gemini (peca leve) — NUNCA outro Claude
- code review / debug profundo -> Codex (gpt-6.1-sol low rotina, high pesado)
- conversao formato -> agy (Gemini Flash)
- multimodal video/audio -> agy **`gemini-4-argon-medium`** (padrão desde 16/10, OK do autor; medição em `<pasta-do-projeto>\_ferramentas\TESTE-ARGON-VIDEO.md`, não incluída; eu nao vejo video). 🔴 **Número, arroba, link, crédito e texto miúdo que ele citar = conferir no QUADRO (ffmpeg + Read) antes de usar**: ele INVENTA com cara de verdade em vez de declarar. Arquivo <100 MB (limite do `view_file`); vídeo longo → proxy `scale=640:-2,fps=2 -crf 30 -ac 1` (64 MB a 24 fps trava em `stream idle timeout`). Corte por timestamp de palavra segue na transcrição, não no olho.

Quando NAO delego:
- microtarefa <30s minha
- voz de cliente especifica (cada cliente tem regra diferente - `<cliente-ótica>` != `<cliente-C>` != canal do autor != `<cliente-B>` != `<cliente-farmácia>`)
- decisao criativa que precisa do feeling do cliente
- verificacao que custaria mais que fazer
- arquitetura cross-arquivo que precisa visao do projeto inteiro
- material `RESTRITO` ainda nao mascarado

---

## REFERENCIAS

**Memorias opcionais (auxiliares, nao obrigatorias):**
- `feedback_orquestracao_multi_llm.md` - historico de regra de tier
- `reference_gemini_codex_cli.md` - historico de instalacao e estado (não incluída)
- `feedback_subagent_critico_padrao.md` - historico de critico em escada
- `feedback_ps1_encoding_acentos.md` - encoding Windows (não incluída)
- `feedback_orquestracao_multi_llm.md` (caso 2026-04-28) - Gemini alucinou em capability check, licao "teste antes de afirmar"

**Docs oficiais:**
- Codex CLI features: https://developers.openai.com/codex/cli/features
- Codex CLI reference: https://developers.openai.com/codex/cli/reference
- Codex non-interactive: https://developers.openai.com/codex/noninteractive
- Codex models: https://developers.openai.com/codex/models
- Codex sandboxing: https://developers.openai.com/codex/concepts/sandboxing

## Regras que sobreviveram (jul/2026)

Histórico datado (versões, incidentes, receita R6) em `ref/historico-2026.md` — ⏳ reconferir antes de usar. Ficam aqui só as 4 regras atemporais:

- **Codex `read-only` só lê texto via stdin:** não lê filesystem nem roda subprocesso; pra isso, `codex-run.ps1` (bypass embutido).
- **Modelo `mini` não segue instrução complexa em contexto grande:** só pra tarefa simples; extração schema-bound pede o modelo cheio.
- **Cota Plus esgota em ~15 chamadas de ~200 KB (⏳ medido jul/2026):** fan-out grande não cabe numa janela → híbrido (Codex até acabar a cota, o resto em Workflow de subagents Sonnet/Haiku).
- **NIM 8B (`meta/llama-3.1-8b-instruct`) pra extração de volume com schema:** modelo quente responde ~1s; o 70B tem cold-start e estoura timeout.
