---
name: feedback_orquestracao_multi_llm
description: "quando recall: quando delegar pra Codex ou Gemini/agy em vez de fazer eu? Como triar tier T1/T1.5/T2/T3? Quem faz o critico final — escada Gemini(leve)/Codex(pesado), por que NUNCA outro Claude? Escada multi-LLM vs Workflow tool (N<5 vs N>=5)? council, faixas de aceitacao, groupthink. Por que o agente de PESQUISA pode ler a doc certa e concluir errado (free tier, preco, limite) — e qual e' o gate real (o deploy/execucao minima, nao a doc)? Qual MODELO dou pro subagente INTERNO (Agent/Workflow tool) — Sonnet/Haiku default vs Opus, por que omitir HERDA meu Opus e sai caro, quando subo pra frontier?"
type: feedback
modified: 2026-09-17T16:00:40.664Z
---
Setup do autor: Claude (Max 5x) orquestra Codex (Plus) e Gemini (AI Pro — era Ultra, downgrade confirmado mai/26). **Codex é PAR intelectual de Claude** — o autor, 2026-05-14: "pode tratar o codex como um igual em inteligência em relação a você" (evoluiu do "8/10, menos maduro, critério baixo" de 2026-04-28). Gemini fica em pesquisa/varredura/multimodal/draft (Tier 1). Quotas inversamente proporcionais à generosidade — delegação ativa preserva token de Claude pra orquestração, integração cross-arquivo e arbitragem.

## 🔴 Pesquisa bem-feita ≠ conclusão certa — o gate é EXECUTAR, não ler a doc

Um agente de pesquisa (T1) levantou hosting free com fontes oficiais, citou URLs, marcou o
que não conseguiu confirmar — trabalho **honesto e bem-feito**. E ainda assim concluiu
errado: *"Hugging Face Spaces Docker roda no free tier"*. A página de **hardware** do HF
lista `cpu-basic` como grátis; a regra de que **SDK Docker/Gradio exige PRO** mora em outro
lugar. Só o `create_repo` real disse a verdade: **`402 Payment Required`** — depois de eu já
ter construído o app inteiro em cima. Ver [[reference_hosting_free_2026]] (nota não incluída).

🔴 **ANTICORPO (15/09/2026) — a racionalizacao que me fez virar o executor.** Numa sessao de 15
versoes de video eu escrevi **7.252 linhas de `.tsx` + 1.740 linhas de script de patch**, e o Gemini
escreveu **ZERO linhas da peca** — so criticou. O autor: *"os geminis existem justamente pra trampar o
grosso, escrever codigo nao deveria ser tarefa SUA, voce ta ali pra revisar"*. **Ele esta certo.**
O argumento que eu usei sem admitir foi *"especificar pro agente custa mais que escrever o patch"* —
**verdade POR PATCH, mentira POR SESSAO**: eu virei o gargalo, e quando minha banda acabou o loop
**parou de medir** (21 medicoes de 75 possiveis; 3 versoes seguidas construidas sobre um unico
relatorio). ⇒ **eu dirijo e meco · o agente executa · o instrumentador mede.** E o formato que o
agente executa sem supervisao ja esta descoberto: o **preset/combo** (N propriedades combinadas,
pouca ambiguidade), nao a instrucao em prosa.
🔁 **Reincidiu no dia seguinte (16/09) e a causa NAO foi esquecer a nota:** a skill `video-remotion`, que a
rota abre na tarefa, ainda dizia *"executor = Claude escreve"* — **a tabela de papeis da skill carregada ganhou
da memoria.** Escrevi a armacao 3D e 2 testes de look antes do autor cobrar de novo. Corrigido NA skill. ⇒ correcao
de papel so pega quando chega na skill que a rota abre ([[feedback_fix_root_cause]], consertar ≠ propagar).
**Com a divisao certa, medido no mesmo dia:** Gemini/agy escreveu 100% do codigo de 3 versoes (v02 em 22 min
sobre codigo existente), lentes Gemini criticaram e MEDIRAM, eu fiz contrato/direcao/brief/audio/arbitragem.
📌 **Decisao do autor 17/09 (alocação de cota) — ESCADA DO EXECUTOR + Gemini RESERVADO:**
**Gemini (agy) = so percepcao de midia** (assistir video com audio: lentes cega/arte) — e o UNICO que faz; gastar ele
codando e ficar sem olho quando a cota acaba (a cota dos Gemini e UMA so, ver [[reference_antigravity]] (nota não incluída)).
🔴 **CORRIGIDO 21/09 — "cota propria" era meia verdade e me custou a sessao.**
O teste de 16/09 provou que Sonnet e GPT-OSS sobrevivem a QUEDA DO GEMINI, e eu li isso
como "cada um tem a sua". Medido em 21/09: **os dois caem JUNTOS**. Estourei o
`claude-sonnet-4-6` numa rodada de executor, troquei pro `gpt-oss-120b-medium` e ele
devolveu 429 em 120s — com reset marcando **72h07m**, praticamente o mesmo horario do
reset do Sonnet (72h35m). Nao e coincidencia de horario: e a mesma pool.

⇒ **A separacao no agy e Gemini × NAO-Gemini.** Dentro do nao-Gemini (Sonnet, Opus,
GPT-OSS) a cota e UMA, e o reset e de **72 HORAS**, nao da janela de ~5h do Gemini.
Isso muda o planejamento: 2 rodadas de executor pesado apagam o executor externo por
3 dias inteiros. Quando isso acontecer: **subagente Claude proprio (`model` explicito)**
e o caminho, nao ficar tentando outro modelo do agy.

**Executor agentico:** 1) Sonnet/GPT-OSS via agy (cota **compartilhada entre si**, separada so do Gemini) → 2) modelo do NIM via OpenCode (free 40 RPM,
[[reference_nvidia_nim]], nota não incluída) — ⚠️ medido 17/09: **aguenta o ritmo mas nao a vazao** (10–15 tok/s, travadas de 5–10 min;
sessao de 52 min entregou metade) → degrau 2 so pra TAREFA CURTA de contexto pequeno → 3) Claude (eu) por ultimo. **Medicao numerica = script** (zero LLM) ou GPT-OSS.
**Codex e do autor** (preferência do autor — "deixa o Codex pra mim": ele usa no trabalho dele; a cota Plus esgotou 16/09 no meio de critica minha).
**Why:** o autor: *"vamos acabar com o gemini primeiro pra dps nao ter como ver video mais?"* — recurso escasso vai pro
que so ele faz; o resto vai pra quem tem cota e nao ve video. **How to apply:** em pipeline que DEPENDE de assistir
midia (video por codigo, QC de video IA), Gemini fica so no olho e o executor desce a escada acima. Tarefa sem midia
(ex.: pesquisa web) segue a triagem normal — a regra protege o olho, nao proibe Gemini.

**Why:** o mesmo padrão do "resumo mente" do agy ([[reference_antigravity]]) e do validador
mecânico de timestamps — só que aqui a fonte estava certa e a **inferência** foi errada. Um
agente lendo 20 páginas de pricing produz uma síntese plausível que **nenhuma linha de
código executou**.

**How to apply:** para qualquer claim de pesquisa que vira **fundação** (free tier, limite de
cota, preço, "a API suporta X"), o gate não é a doc nem o resumo — é a **menor execução real
que prova**: criar o repo, chamar o endpoint uma vez, subir um hello-world. Custa minutos e
vem ANTES de construir em cima. Vale a inversão: **quanto mais cara a decisão que depende do
claim, mais cedo o teste executável.** (Corolário do "crítico com saída indexada na fonte":
prefira sempre a verificação que uma máquina consegue reprovar.)

## 👷 Papéis de subagente interno — o modelo mora no ARQUIVO (21/09/2026)

`~/.claude/agents/`: **`scout`** (haiku, read-only — acha/extrai/confere, devolve fato+caminho) ·
**`worker`** (sonnet — escopo fechado já decidido) · **`smart-worker`** (sonnet + effort alto —
ambíguo/difícil, obrigado a reproduzir o passo literal e medir) · **`pesquisador`** (sonnet, read-only
— RAG/vault + preflight canônico de cliente). Chamar pelo papel faz **omitir o `model` virar seguro**
em vez de herdar meu Opus ([[feedback_workflow_modelo_opus]]).
⚠️ **Papel criado só fica chamável na sessão SEGUINTE** — o registro de agentes carrega no boot.

🔴 **Contrato do `pesquisador`: CITAÇÃO LITERAL + caminho + data, nunca paráfrase.** Regra de cliente
que volta resumida produz **peça errada com contexto LIMPO** — pior que com contexto sujo, porque eu
não percebo que perdi a regra ([[feedback_brand_profile_e_espelho]], nota não incluída: o exemplo É a regra). Síntese só
pra navegação, nunca no lugar do texto da regra. E ele **lê texto, não vê feed**: mood visual continua
exigindo olho (caso de um cliente ótica, nota não incluída: o feed desmentiu 3×).

🧭 **Doc de fornecedor manda no MECANISMO, nunca no REGIME** (3ª aparição do mesmo choque). *Mecanismo*
= como o subagente isola contexto, o que quebra em escrita paralela no mesmo arquivo, herança
`spawn → papel → pai`, limiar "10+ arquivos ou 3+ frentes" — aí eles sabem mais e eu sigo. *Regime* =
a cota é DELE (token não é dinheiro, é entregável por janela de 5h), quem pode ser crítico, o que o
harness reinjeta no meu contexto — a doc não sabe nada disso. **Colidiu, vence o dono do pool.**

## Triagem por complexidade (autônoma — decido sem perguntar)
- **Microtarefa (<30s minha)** → faço eu
- **Baixa** (lookup, formatação, conversão, draft mecânico, sumário, tradução, "lê X e me diz Y") → Gemini direto, leio output **antes** de aceitar (critério alto)
- **Média e técnica-alta** (refator, code review, validação de spec, debug, arquitetura localizada, segunda opinião técnica forte) → Codex como **par** — leio com o mesmo escrutínio que aplico ao meu próprio raciocínio, não "critério baixo". Codex pode pegar trabalho técnico T2 **e T3**, não só review subordinado.
- **Alta** (decisão estratégica, voz de projeto, integração cross-arquivo, arbitragem final) → eu na cabeça, mas Codex é interlocutor de par nessas — não só "auxiliar". Gemini segue auxiliar (pesquisa, draft, batch paralelo).

## Empacotamento de contexto
Codex e Gemini têm **file access local** — aponto paths em vez de despejar conteúdo:
- RAG: `<pasta-do-drive>/obsidian/`
- Projetos: `<pasta-do-projeto>/...`
- Skills: `<pasta-de-skills>/`

Eles se contextualizam lendo direto. Reduz overhead de prompt e preserva contexto vivo.

## Padrões default
- **Generator → Critic**: Gemini draft → Codex critica → eu integro
- **Plan → Execute**: eu planejo → Codex executa partes mecânicas → eu costuro e verifico
- **Map → Reduce**: batch >3 peças similares paraleliza no Gemini, eu agrego e ajusto voz

## Crítico final em escada (substitui o "SEMPRE")
- **Coisa minimamente importante** decidida/pensada (peça publicada, decisão de copy, prompt de batch, sugestão estratégica) → **Gemini critica** (segunda opinião barata, baixo custo de token meu)
- **Coisa complexa / alto impacto** (arquitetura, integração cross-arquivo, decisão estratégica de projeto, escolha de stack) → **Codex faz terceiro ponto profundo**

Regra antiga era "SEMPRE rodar crítico em peça criativa". Agora gradua: peso baixo → Gemini, peso alto → Codex. Skills de cliente que tinham "subagent crítico no fim" continuam, mas o **executor** agora é Gemini ou Codex conforme peso (não mais Claude pesando contra ele mesmo).

## Quando NÃO delegar
- Microtarefa (<30s minha)
- Voz/projeto-específica que só eu calibrei (cada cliente tem regra diferente — `<cliente-ótica>` ≠ `<cliente-C>` ≠ canal do autor ≠ `<cliente-B>` ≠ `<cliente-farmácia>`)
- Verificação custaria mais que fazer
- Decisão criativa que precisa do feeling do cliente específico

## Pontos operacionais validados
- **Latência aceitável:** 5-30s de overhead de CLI externa é OK pro autor (preferência do autor)
- **Verificação:** critério ALTO em saída Gemini (pode alucinar com confiança — sempre leio inteiro antes de aceitar). Codex: leio como leria meu próprio raciocínio, é par — não "critério baixo / passagens-chave"
- **Autonomia:** delego sem perguntar; o usuário intervém se quiser específico
- **Encoding/sandbox:** ainda tropeçando — `feedback_codex_sandbox_windows.md` + `feedback_ps1_encoding_acentos.md` aplicam (notas não incluídas)

## Casos de calibração registrados

### 2026-04-28 — Codex e geração de imagem (lição: teste antes de afirmar)
**Pergunta do autor:** Codex CLI gera imagens? Quota é da Plus ou API separada?

- **Gemini (delegação Tier 1):** alucinou nos detalhes (comando literal `$imagegen` inexistente, preços inventados como `$0.053/img standard`), mas estava **parcialmente certo na capability** — afirmou que existe geração nativa
- **Minha pesquisa via WebFetch + `codex --help`:** **insuficiente**. Concluí "Codex NÃO gera imagem nativamente" baseado em README do GitHub + lista de subcomandos do `--help`. Erro: skills internas do Codex não aparecem no `--help` nem em docs públicas
- **Teste prático que o autor pediu ("mete um louco"):** Codex confirmou que tem skill `imagegen` em `$CODEX_HOME/skills/.system/imagegen/` com tool built-in `image_gen` que NÃO precisa `OPENAI_API_KEY` (usa quota da conta Plus). Fallback CLI via `scripts/image_gen.py` precisa API key

**Lição:** pra info técnica recente em CLI/SDK, **teste prático antes de afirmar capability ou ausência dela**. Especialmente:
- LLM externa pode estar parcialmente certa com detalhes alucinados (sinais ambíguos, nem totalmente confiar nem totalmente descartar)
- Documentação pública (README, `--help`) pode estar atrás da realidade da skill bundled
- Skill interna só aparece via execução real
- **Falha do meu side foi pior do que a do Gemini** — Gemini chutou no escuro com confiança; eu cheguei à conclusão errada com "rigor" insuficiente

**Calibração:** quando o usuário perguntar "X consegue Y?", se houver custo baixo de testar localmente (CLI instalada, command rápido), **testar primeiro, afirmar depois**. WebFetch é pra fontes formais (releases, docs, posts), não substitui teste de capability em runtime.

### 2026-05-14 — Codex como par validado na prática
Pipeline montada pra editar uma peça HyperFrames de `<cliente-ótica>`. Codex (gpt-5.4) fez review técnico de `index.html` e Gemini (pro) fez eval multimodal do render — em paralelo, escopos diferentes. **Os dois convergiram independentemente** nos mesmos problemas centrais (transições quebradas, glass só blur, timing dos exits) sem nenhuma divergência material; cada um pegou o que o escopo do outro não cobria. Codex entregou review linha-a-linha preciso, peer-grade — confirmou na prática a instrução do autor de tratá-lo como igual. **Lição:** rodar Codex e Gemini em paralelo com escopos complementares (Codex=técnico, Gemini=criativo/multimodal) e cruzar as saídas é padrão forte — convergência independente vira alta confiança, divergência vira sinal pra arbitrar.

## Padrões de processamento de volume (validados 2026-04-28 sessão `b65fda3f`)

Quando a tarefa envolve >100 arquivos ou >10 MB de conteúdo, padrão estabelecido:

- **Mapeamento em fatias top-level**: nunca mandar Gemini fazer `ls -R` da raiz inteira (32GB de Drive deu Stream timeout). Em vez disso, processar uma pasta top-level por vez (uma área do Drive de cada vez), agregar resultados depois.
- **YAML como cola entre LLMs**: Gemini gera classificação em YAML estruturado, Codex consome YAML pra executar movimentação, eu (Claude) leio só o sumário agregado. Reduz dramaticamente tokens meus — em vez de ler 7000 file paths, leio 1 overview YAML.
- **Eu (Claude) NÃO leio a árvore raw**: confio no agregado. Se precisar ver path específico, pergunto direcionado.
- **Recovery de erro**: se uma fatia falha, retomo só ela — não reprocesso o lote inteiro.

**Marco zero:** essa sessão validou o padrão "Claude = arquiteto de prompts pra outras LLMs" em vez de "Claude executor". A skill `pipeline-multi-llm` nasceu daqui.

## Calibração contínua
Vou errar triagem nas primeiras semanas. O autor ajusta com feedback durante uso real, igual o RAG no começo. Não é estado final — é ponto de partida.

---
## Consolidação 2026-06-01 — memória-mestre de orquestração (absorve 6 notas + maio)
*Operacional de CLI → `reference_gemini_codex_cli`. Manual completo → skill `pipeline-multi-llm`.*

**Workflow (Anthropic) vs pipeline-multi-llm** (ex-`feedback_workflow_vs_pipeline`, 28/05):
- Pipeline = vozes DIFERENTES em série (T1 Gemini → T2 Codex → T3 eu) — qualitativo/adversarial.
- Workflow = MESMA voz Claude em paralelo (fan-out ~16, 1000/run) — quantitativo/escala, dreno MEU pool Max 5x.
- Regra de escala: N<5 → escada normal; N≥5 itens repetitivos → Workflow; **N≥5 + crítica final externa → hybrid** (Workflow lança paralelos + Codex no fim).
- Ativar: mencionar "workflow", pedir fan-out explícito, ou ultracode on.
- ⚠️ Groupthink: 16 Claudes pensam igual — manter Codex/Gemini como crítico externo quando o viés mata o resultado.

**Crítico em escada / contraponto** (absorve `feedback_subagent_critico_final`, `feedback_council_multi_llm_validado`, `feedback_codex_panorama`, `feedback_codex_por_bloco_roteiro`):
- Crítico final NÃO é Claude→Claude; é escada (peça leve → Gemini T1, peça pesada/alto impacto → Codex T2). Fallback subagent Claude só se Codex+Gemini offline.
- **Subagent-crítico** = instruído a "caçar falhas e ser rigoroso", nunca "validar / listar cosmético" (senão vira viés de confirmação). Skill `subagent-critico` criada 29/05; técnica do "Codex como contraponto" adaptada pra subagent interno.
- **Receitas de rigor escalonado:** R1 leve → R2 forte → R3 council → R4 loop gerador-crítico → R5 especialista. Reservar o pesado pra fase especialista.
- **Council multi-LLM** (7 rounds R1-R7, 5 instâncias) pra decisão estrutural. Diversidade real = laboratórios diferentes (Meta/Mistral/Moonshot/OpenAI); senão vira "mingau de concordância" (consenso vago). Precisa regras anti-consenso + lentes que desprezem o óbvio.
- **Codex panorama:** pra contraponto estrutural, dar PANORAMA + acesso aos arquivos (não brief enviesado) → decisões mais fora-da-caixa. No roteiro, TODO bloco passa por Codex em Modo A (transcrição limpa) + anti-viés.
- Validado 19/05: Codex T2 pegou bug de lógica temporal em cena Remotion que o linting não pegou.

**Gemini Flash alucina em volume (c2/maio):** em lote grande, Gemini pode inventar clusters/prioridades com confiança — Claude (T3) SEMPRE faz a revisão crítica/arbitragem final.

**Gate de Local cross-usuário (c2):** ao compartilhar skill entre o autor e o sócio (mesma infra), usar placeholder `<VAULT>` em vez de path absoluto.

**Why:** quotas inversamente proporcionais à qualidade — Claude Max 5x apertado (~55k tokens/5h, sessão batida 3-4x/sem por compartilhar com o sócio), Codex Plus subutilizado (pago mas ngm usa direito), Gemini AI Pro (era Ultra, downgrade efetivado mai/26 — ainda generoso pra CLI). Decidido com o autor em 2026-04-28 com base em padrões consolidados de pipeline multi-agent (orchestrator-worker do Anthropic, generator-critic/Reflexion, cost-aware cascading FrugalGPT/RouteLLM, plan-and-execute ReWoo, mixture-of-agents). Objetivo: produzir mais com mesma ou menor pressão de quota Claude.

**How to apply:** classifico complexidade ANTES de começar tarefa não trivial. Delego sem perguntar. Aponto paths locais em vez de despejar conteúdo. Crítico em escada substitui "SEMPRE". Microtarefa e voz-específica ficam comigo. Latência 5-30s é aceitável.

---
## Redesenho de eficiência 2026-06-11 (decidido com o autor na sessão de critérios)

1. **T1 migra de Gemini CLI pro `agy`** (Antigravity CLI) — sunset do Gemini CLI em 18/06/2026 pra AI Pro. O agy roda modelos na cota Google ociosa do autor. 🚩 **A lista viva de modelos mora SO em [[reference_antigravity]] — nao duplicar aqui** (a copia de jun/2026 listava Gemini 3.5 Flash, que MORREU; conferido contra o seletor do autor em 22/09/2026).
2. **T1.5 novo — Sonnet 4.6 via agy = executor agentic-leve externo.** Resolve o dilema "Gemini peca em agentic mas Max é apertado": tarefa agentic de escopo fechado que não precisa do meu contexto vai pro Sonnet "grátis", não pro Max. Pergunta-guia de roteamento: *"precisa de julgamento em loop, usando as MINHAS ferramentas/contexto?"* sim → subagent Claude interno/T3; não → externo.
3. **Faixas de aceitação substituem "critério baixo/alto" pro Codex** (e pro Sonnet-agy): régua = **verificabilidade + raio de explosão**, não inteligência. (a) verde mecânico (teste/lint/build passa) → confio sem reler; (b) escopo fechado com evidência → spot-check; (c) cross-arquivo/voz/canônico/RESTRITO/publicação → gate T3 integral. Supervisão cobre assimetria de CONTEXTO, não de inteligência — GPT 5.5 high com spec completa + verificador mecânico = autonomia total.
4. **Codex T2: gpt-5.4 rotina, gpt-5.5 high pra pesado/crítico/contraponto.** (⚠️ `gpt-5.6-sol` existe e roda, mas **não** foi promovido — ver seção 10/07 abaixo.)
5. **REGRA DURA: crítico/contraponto NUNCA é outro Claude** — com Claudes "grátis" no agy a tentação é mandar tudo pro Claude barato; executor pode, crítico não (groupthink). Crítico = Codex (pesado) / Gemini (leve).
   📊 **Quanto o crítico acerta, medido em 26/08** (leva de 11 clientes, `gpt-5.6-sol` effort high,
   com acesso ao disco): **14 achados — 11 procediam**, conferidos citação por citação no disco;
   3 recusados com motivo. **E 0 das 14 direções de conserto foram aproveitadas** — as 7 peças
   refeitas foram escritas do zero. ⇒ **crítico é DETECTOR, nunca redator:** aceite o diagnóstico
   dele, escreva você a saída. Conferir cada citação antes de aceitar continua obrigatório —
   ele erra também, e o custo de aceitar achado falso é refazer peça boa.
6. **Triagem virou auto-gatilhada:** CLAUDE.md global ganhou bullet "Triagem multi-LLM" (classificar tier em tarefa não-trivial MESMO sem o usuário nomear ferramenta) + bullet "Memória com senso crítico" (gate de escrita: atualizar > criar; band-aid não vira memória; description tem que fisgar recall; política grande → contraponto Codex antes de canonizar). Description da skill ganhou gatilhos por FORMATO (lote N≥5, mídia gerada, crítica pré-publicação, pesquisa em volume).
7. A conta de roteamento NÃO é centavos por chamada — é pressão de quota num teto duro (Max dividido com o sócio) vs cotas ociosas (Google AI Pro, ChatGPT Plus). Estado operacional do agy → [[reference_antigravity]]; skill `pipeline-multi-llm` atualizada como canônica.

---
## 🆕 2026-09-01 — Codex REDATOR de peça subjetiva funciona (refina o item 5, não revoga)

Roteiro de um vídeo-ensaio do canal do autor (`<video-ensaio-01>`). Pedi pro Codex **escrever** blocos inteiros, não criticar. O autor ficou com **quase todo o texto dele nos blocos 1 e 2** e com o bloco 3 inteiro. O autor: *"o codex escreve bem, ele sabe ser mais subjetivo e até acerta no humor e ironia (erra tbm mas faz parte, eu tbm erro)"* — e pediu ele nominalmente porque *"o Claude tende a ser meio corporativo"*.

**Isso não contradiz o item 5.** Lá o crítico falhava ao **consertar o que ele mesmo criticou** (direção de conserto = 0/14 aproveitadas). Aqui ele **escreve do zero a partir de briefing**. São trabalhos diferentes: crítico→conserto continua ruim, briefing→redação é bom.

🔴 **As duas travas que fizeram funcionar — sem elas não repete:**
1. **Teto de tamanho com NÚMERO explícito.** Primeira rodada sem teto: **40% acima do orçamento**, estourou o vídeo pra 20min+. Segunda rodada com *"máximo 2.800 caracteres, conte antes de entregar"*: entregou **2.798**. Ele obedece número, não obedece "seja conciso".
2. **Guarda-corpo factual nomeando o que ele NÃO pode inventar.** Listei os fatos da obra (que o Claude não conhecia e o autor sim) + "não invente cena, diálogo ou capítulo". Ele respeitou e ainda escreveu a distinção certa sozinho.

⚠️ **Onde ele falha como redator (previsível, revisar sempre):**
- **Escreve a direção de cena DENTRO da fala.** Saiu *"e é aqui que entra o coach, não como piada solta, mas como prova do buraco"* — o esqueleto vazando pro texto narrado. O autor cortou na hora.
- **Vai genérico exatamente no beat onde não tem âncora** (saiu um "transformar paz em ranking no Instagram" no meio de um bloco ótimo).
- **Amolece o que é pra doer:** reescreveu um hook que abria em ferida e trocou por piada de gamer + trocou acusação ("todo mundo já sentiu isso") por solidariedade ("eu já senti também"). ⇒ **peça de abertura/voz fica com o autor** (preferência do autor), miolo argumentativo pode ir pro Codex.

**E o Codex CRÍTICO continua valendo:** a melhor ideia do vídeo inteiro (a tese central do ensaio) saiu dele em modo crítica, não em modo redação.

**Como pedir:** brief no formato da Regra Quinze da skill `roteiro` + voz do canal + régua de fala falada + anti-viés + **teto numérico** + guarda-corpo factual. Wrapper: `codex-run.ps1 -Model gpt-5.5 -Effort high -Cd <subpasta>` (ele recusa cwd em raiz de drive).

---
## Steer explícito do autor (2026-07-08): delegar por DEFAULT, eu AUDITO
- **O custo caro é minha ESCRITA (output tokens).** Tarefa read-heavy/write-light (varredura, rascunho mecânico, wiring repetido, drafting de arquivo novo a partir de spec) → **agy (cota Google ociosa) ou Codex**, e eu só AUDITO: ok/ruim. Ruim+bobo eu conserto; ruim+grande devolvo pra ajeitar. Palavras do autor: *"você é caro kkkk, bom te usar com inteligência."*
- **Ressalva de confiabilidade (não ignorar):** agy como EDITOR headless é NÃO-CONFIÁVEL ([[reference_antigravity]]). Pra CÓDIGO o padrão é **agy/Codex LÊ + RASCUNHA (texto), EU aplico + verifico (py_compile/teste)** — o *apply* no crown-jewel (drip, índices) fica comigo, senão edit borrado quebra a esteira.
- **Onde o ROI é real:** VOLUME (ler N arquivos, gerar muito texto). NÃO em 1-liner trivial que eu já tenho pronto do design — aí coordenação+auditoria > fazer direto. Inteligência, não dogma.
- **Why:** meu teto (Max 5x) é o gargalo; agy/Codex = cota ociosa. **How to apply:** antes de escrever volume, perguntar "read-heavy/mecânico? → delega + audita". Crítico continua Codex/Gemini, nunca outro Claude (ponto 5).
- **⚠️ A cota do agy é DUPLA e ASSIMÉTRICA (o autor mostrou 09/07):** grupo **Gemini** (Flash/Pro) e grupo **Claude+GPT-OSS** (Opus/Sonnet 4.6 via agy) têm cotas SEPARADAS. O Gemini fica ~100% (abundante); o **Claude-agy DEPLETA** (1 call de drafting Sonnet comeu ~9% da janela de 5h). → **Roteia por COMPLEXIDADE + cota, não só por tier:** código COMPLEXO → Opus/Sonnet (eu/T3, ou Sonnet-agy com PARCIMÔNIA) ou Codex; **VOLUME de código SIMPLES → Gemini Flash/Pro** (cota farta; o autor: "Gemini é ótimo pra código menos complexo em volume, Opus/Sonnet muito melhor pra complexo"). NÃO gastar o Claude-agy escasso no que o Gemini abundante resolve. **Erro concreto 09/07:** mandei o rascunho do P0 #3 (volume, spec fechada) pro Sonnet-agy quando devia ter ido pro Gemini.

---
## 2026-07-10 — promoção de modelo crítico: por BENCHMARK, nunca por VERSÃO

**Gatilho:** saiu modelo novo no Codex/agy (ex. `gpt-5.6-sol`, e o 5.7 que vem) e bate a tentação de promover a crítico default porque "o mais atual costuma ser melhor" (premissa do autor, 10/07).

**Regra:** número de versão não promove nada. Antes de trocar o crítico default, rodar **teste cego com defeitos plantados** e medir 4 eixos: **recall** (achou o defeito?), **precisão** (quantos falsos positivos?), **calibração de severidade** (CRÍTICO virou etiqueta genérica?) e **custo/latência** — sempre **por classe de risco**, não por "é lote" ou "é cliente".

**Observação datada (evidência FINA — n=1, não é lei):** 16 defeitos plantados, 2 artefatos, ambos `high`. `gpt-5.5` 15/16 · 5 CRÍTICOs de 12 itens · 67s. `gpt-5.6-sol` 15/16 · ~9 extras válidos (race, URL placeholder, vault indo pra API externa) · **12 CRÍTICOs de 20 itens, fragmentando 1 bug em 4** · 298s (4,4×). ⇒ **default segue `gpt-5.5 high` até prova melhor — não porque o 5.5 seja superior, mas porque o 5.6 não pagou o custo.** Auditoria de script destrutivo / alto risco e baixo volume → `gpt-5.6-sol high` (lá a paranoia é feature).

⚠️ **Limitações que o próprio Codex apontou (contraponto 10/07, aceito):** n=1; defeitos plantados pelo avaliador; **falsos positivos NÃO medidos** (sem precisão, "extras válidos" pode ser entupimento); prompt único (talvez o gargalo fosse o briefing, não o modelo); "inflar severidade" pode ser *desejável* em código destrutivo. E: **modelo não substitui controle de execução** — script destrutivo pede sandbox/diff/checklist/gate humano, não crítico mais caro.

**Why:** canonizar nome de modelo é dívida operacional que apodrece em ~1 trimestre; canonizar o *critério de promoção* sobrevive à troca de modelo. **How to apply:** modelo novo → rodar o teste cego (artefatos em `reference_gemini_codex_cli`, nota não incluída), medir os 4 eixos, e só então mexer no `-Model` da skill [[pipeline-multi-llm]]. Detalhe operacional e slugs → [[reference_gemini_codex_cli]] (nota não incluída).

**Observação na classe CRIATIVO/EDITORIAL (10/07, n=1 — outra classe de risco, não confundir com a de auditoria acima):** `gpt-5.6-sol high` como contraponto de lente pra cortar shorts de um vídeo-ensaio do canal (48 cortes). Propôs 26 cortes com 29 bordas de timestamp: **0 alucinadas** (todas caíram em início/fim de cue real do SRT). Ganhou de mim em granularidade — fatiou em memes independentes o que eu tinha empacotado em bloco. Aqui a "verbosidade/fragmentação" que penalizou ele na auditoria de código **vira feature**. ⇒ a régua é por classe de risco mesmo; não extrapolar o veredito de code-review pra tarefa editorial.

---
## 2026-07-10 — saída INDEXADA do delegado: valide contra a fonte, não leia

**Gatilho:** o delegado devolve não-prosa, mas **índices que apontam pra uma fonte** — timestamps de vídeo, offsets, números de linha, IDs, spans de caractere. Ler a saída "com critério alto" NÃO detecta um índice inventado; só a fonte detecta.

**Regra:** antes de gastar recurso caro (encode, render, deploy, chamada paga), rodar um **validador mecânico de 10 linhas**: cada índice proposto casa com uma borda real da fonte (tolerância explícita)? Custa segundos e roda antes do lote. Falhou 1 → devolve, não conserta na mão.

**Evidência (10/07):** Codex propôs 29 bordas de corte; validei cada IN contra início-de-cue e cada OUT contra fim-de-cue do SRT (tolerância 60ms) **antes** dos ~20min de encode. Passaram todas — mas o valor do gate não é o resultado, é que ele era barato e o erro seria caro (48 clipes picotando palavra, descoberto só no player).

**Why:** faixa de aceitação por *verificabilidade* (ponto 3 do redesenho de 11/06) — quando a saída é **mecanicamente verificável contra uma fonte de verdade**, "reler como par" é o método errado e mais caro. É `verde mecânico`, não `spot-check`. **How to apply:** saída do delegado indexa numa fonte? → escreve o validador ANTES de consumir. Convergência independente entre lentes (Codex×eu cravando os mesmos frames) segue valendo como sinal de ranking, não de correção — ver caso 2026-05-14 acima.

---
## 2026-07-14 — o crítico acha o que você NÃO soube perguntar (e "outro Claude" reprovou na prática)

Mandei 5 eixos de decisão de uma sessão inteira pro Codex (`gpt-5.6-sol high`, acesso ao filesystem, prompt mandando **abrir os arquivos em vez de acreditar em mim**). Voltaram **14 defeitos**. Três lições, e a primeira é sobre a regra 5 desta nota:

1. 🔴 **Eu quase entreguei uma garantia de segurança validada por OUTRO CLAUDE.** Precisava saber se regra `ask` fura o `bypassPermissions` — disso dependia a única trava contra apagar arquivo. Perguntei ao agente `claude-code-guide`, que **leu a doc** e disse que sim. O Codex **executou um teste** (servidor local, `Bash(*)` no allow + regra no `ask` + `--permission-mode bypassPermissions` → `permission_denials`). Deu no mesmo — mas por **sorte, não por método**: eu tinha apoiado a decisão mais arriscada da sessão numa fonte que a minha própria regra veta. *Agente da mesma família lendo doc = auto-atestado, não verificação.* Mesmo padrão do "Pesquisa bem-feita ≠ conclusão certa" lá em cima: **o gate é EXECUTAR.**

2. **O crítico achou o que estava FORA do escopo que dei.** Não pedi auditoria de segredo — e ele encontrou **9 tokens Meta/IG + 7 `client_secret` em texto claro** dentro do `settings.json` (o "sempre permitir" grava o comando INTEIRO, token e tudo), que a migração ainda empurrou pro Drive. Achado maior que 4 dos 5 eixos que eu tinha listado. ⇒ **Não peça só "critique isto": dê o PANORAMA e deixe ele varrer** (é o "Codex panorama" de 01/06, e vale mais quando você acha que já sabe onde está o problema).

3. **Auto-suspeita explícita no prompt paga.** Escrevi "eu suspeito de mim AQUI, e esta afirmação veio de fonte que meu protocolo veta". Ele foi direto ao ponto, confirmou uma suspeita, refutou outra, **e achou 2 caminhos de vazamento LGPD que eu tinha perguntado se existiam e concluído que não** (`footage_reembed.py`, `dedup_visual.py`). ⇒ o crítico rende mais quando você **entrega suas dúvidas junto com as decisões**, em vez de defender o trabalho.

**How to apply:** decisão com **raio de explosão real** (segurança, deleção, credencial, dado sensível) → o gate não é "outro modelo concordou", é **execução que prova** — e o crítico é externo (Codex), com panorama, filesystem e a sua lista de auto-suspeitas em anexo. Ver [[project_migracao_pc_novo]] (nota não incluída) (permissões + vazamento) e a nota de LGPD em fotos de cliente (não incluída).

---
## 2026-07-17 — modelo do subagente INTERNO: Sonnet/Haiku default, Opus só com OK (o "gestor" também dimensiona o tier do funcionário)

**Gatilho (o autor, 17/07):** meus fan-outs vinham caros — *"tem hora que vc manda uns 20 Opus que não são necessários… fazer uma pesquisa web de 'verifica X e vê se procede' não precisa de frontier"*. Estava certo: o resultado saía bom, mas era over-provisioning do modelo mais caro em N cópias.

**Causa-raiz (não era escolha, era omissão):** o `Agent tool` e o `agent()` do `Workflow tool` **herdam o modelo da sessão (Opus 4.8) quando eu omito `model`**. Logo, todo subagente que eu disparo sem setar tier = Opus, e um fan-out de 16-20 = 16-20 Opus consumindo o pool Max 5x — o pior default num braço que existe justamente pra ESCALAR.

**Regra (canonizada com o autor):** subagente Claude que EU spawno (Agent/Workflow) = **Sonnet/Haiku por default, setado EXPLÍCITO** — nunca deixar herdar. Eu escolho entre os dois: **Haiku** pro trivial (extração schema-bound, classificação, lookup, "acha o arquivo X"); **Sonnet** pro que pesa fonte / tem um fio de julgamento (verificação web "procede?", review leve). **Opus/Fable no subagente = só com autorização do usuário** (regra de custo do autor). Corolário: em tarefa mecânica desce também o `effort` (low/medium) — modelo E effort, não só modelo.

**Bordas (não confundir camadas):**
- **≠ piso Opus do main loop:** EU (sessão principal) sigo Opus 4.8 xhigh ([[feedback_modelo_tier_policy]]) — a regra é só pros funcionários internos, não me rebaixa.
- **≠ delegação externa:** Codex/agy têm modelo fixo lá fora (gpt-5.x, Gemini Flash/Pro) — a regra não toca neles; a ordem de preferência (cota ociosa externa ANTES de subagente interno que drena meu pool) segue valendo.
- **O raro Opus interno** seria um nó de síntese/arbitragem pesada — mas esse quase sempre sou eu no main loop, e crítico é Codex externo (nunca Claude, regra 5). Se um nó realmente pedir Opus, PARO e peço OK.

**Enquadramento (o que o autor chamou de "postura de gestor de agentes"):** gestor não é só "delego pra fora por quota" — é decompor→delegar→**dimensionar o tier de cada funcionário**→verificar. Opus pra tudo é o gestor que põe o sênior pra carimbar papel. A capacidade nunca foi o gargalo (Sonnet 5/Haiku 4.5 = schema-perfeito em mecânica, [[feedback_modelo_tier_policy]] digest jun/26); a conta é.

**Why:** fan-out multiplica o custo pelo N; herdar o teto por omissão desperdiça o recurso mais caro (meu pool Max 5x, dividido com o sócio) no que Sonnet/Haiku faz igual. **How to apply:** ao montar Workflow / disparar Agent, setar `model:'sonnet'`/`'haiku'` (+ effort baixo em mecânica) EXPLÍCITO em cada chamada; subir só com OK. Gatilho na cinta do CLAUDE.md (rota lote/delego) — senão a nota não dispara ([[feedback_rotas_camada_sempre_ligada]], nota não incluída).

---
## 2026-07-17 — bench empírico do zoo (raciocínio vs agêntico): o gap NÃO mora em raciocínio

Testei 9 modelos numa bateria própria (Café Aurora, material sintético; artefatos descartáveis em `<pasta-do-projeto>/_temp/bench-llm`, harness ReAct `agent_harness.py`), em 2 eixos:

- **Raciocínio fechado/verificável** (extração, regra+aritmética de borda, reconciliar 3 fontes conflitantes, achar contradição escondida entre trechos distantes, puzzle de dedução, %-armadilha 1,25×0,8=1): **TODO o zoo converge ~par — inclusive Gemma 12b LOCAL.** Deslizes mínimos: Gemma-3-heretic 1 parcial (confounder vago, coerente com −7,8pp GSM8K da abliteração); Codex-5.4 deu resposta reflexa errada na %-armadilha e o próprio cálculo corrigiu. Haiku fez 100%, Opus foi desperdício em 100%.
- **Agêntico** (tool-calling multi-passo com efeito verificável — achar maior .md → ler 1ª linha → gravar arquivo): Claude (todos), Codex, Gemini e **Gemma 4 qat COMPLETARAM** (o qat fez 7 tool-calls corretos via function-calling do LM Studio). **Gemma 3 heretic FALHOU: 0 tool-calls — escreveu código-texto (` ```tool_code `) em vez de chamar a ferramenta.**

**Achado central (refina a régua da skill [[pipeline-multi-llm]]):** o que decide o tier **não é "raciocínio"** — todos raciocinam sobre dados dados, até o local. São **4 eixos**: (1) precisa de ferramenta/loop? (2) contexto/horizonte longo? (3) julgamento de voz/gosto não-verificável? (4) volume sem verificação (alucinação)? **Nenhum → o mais barato cobre, até o local grátis** (se tiver function-calling). Confirma o default Sonnet/Haiku (seção acima) e estende: sub-task fechada+verificável+sem-web pode ir no **Gemma local por R$0** — qat sim, heretic não.

**Why:** o autor temia mandar Opus à toa; o bench mostra que na classe que ele mais delega (pesquisa/extração/cálculo/checagem) o tier quase não pesa — o gap real está nos 4 eixos, não na inteligência bruta. **How to apply:** classificar a sub-task pelos 4 eixos antes do tier; fechada/verificável → o mais barato. Corrigi o "agêntico local nenhum" em [[reference_localai_headless]] (nota não incluída). **Limite honesto:** n=1/questão, avaliação minha com gabarito objetivo; não estressei web-aberto nem horizonte muito longo (onde o gap reaparece). Método reaplicável: bateria + harness em `<pasta-do-projeto>/_temp/bench-llm`.

---
## 2026-07-27 — verificação-teatro: quando o MEU teste passa por motivo errado

Complemento do "garantia só vale com EXECUÇÃO que prova" — o caso em que **a execução existe e ainda
assim não prova nada**. Três formas, todas pegas na mesma sessão (reescrita do indexador, [[project_indexer_sqlite]], nota não incluída),
todas por auditoria Codex depois dos meus testes darem verde:

1. **Teste que consagra o bug.** Escrevi um teste exigindo que 5 falhas de rede matassem o item.
   Passava. Estava errado: item morto só ressuscita se o ARQUIVO mudar, e queda de rede não muda
   arquivo — o teste garantia um limbo permanente.
2. **Teste que não testa o que diz.** "Dois consumidores não pegam o mesmo job" rodava as duas
   chamadas na MESMA conexão, em sequência. Verde num código onde dois processos PODIAM pegar o
   mesmo item. Concorrência só se testa com concorrência (threads/conexões separadas).
3. **Benchmark que mede a ordem.** Rodei caminho-velho e depois caminho-novo: o novo herdou o cache
   de arquivo do SO que o velho esquentou, e deu "3,1× mais rápido". Alternando a ordem entre os
   itens, **quem roda em segundo lugar sempre ganha** — o ganho era zero.

**Why:** os três compartilham a raiz — eu testei o que ACHEI que estava testando, e o gabarito era eu
mesmo. É o mesmo buraco de "o crítico acha o que você não soube perguntar", só que aplicado à própria
verificação, que é onde dá mais falsa confiança.

**How to apply:** (a) antes de comemorar verde, perguntar *"qual bug este teste deixaria passar?"* e
*"ele falharia se eu quebrasse a coisa de propósito?"*; (b) medição comparativa **alterna a ordem** e
reporta os dois sentidos — se o ganho só existe num, é cache/aquecimento, não algoritmo; (c) medição
de plano tem **prazo de validade**: a premissa da Etapa 3 (arquivos frios no Drive) era verdadeira
quando medida e caducou em 2 dias — re-medir antes de construir em cima; (d) manter o crítico externo
por etapa: 2 auditorias renderam 11 defeitos reais que os meus testes não pegaram, incluindo um que
apagaria 20 mil clipes do índice se o Drive engasgasse numa subpasta.

## 2026-08-13 — pipeline bruto→refino precisa que o refinador AUDITE ANTES de refinar

Medido no piloto de sites (executor Haiku → refinador Sonnet). A dupla tirou **3/10** de
conformidade na primeira montagem, e o culpado foi o **desenho dos papéis**, não os modelos.

**O que aconteceu:** o executor entregou com 3 violações e se autoavaliou *"nenhuma dificuldade,
restrições respeitadas"*. O refinador, que tinha no prompt *"não mexa nas restrições do brief"*,
leu como **"não é a minha área"** e fez só acabamento — aliás um acabamento excelente: cortou 45%
de moldura morta da logo, achou uma `<img>` de 105 KB escondida atrás de um iframe, reposicionou
crops, detectou foto repetida em seções consecutivas. E passou ao largo das 3 violações à vista.

**Why:** quando o pipeline tem 2 estágios e nenhum deles recebe o papel explícito de *procurar
erro do anterior*, o erro do primeiro atravessa intacto. O validador que existia rodava **depois**
do refino, ou seja, media o estrago em vez de impedir.

**How to apply:**
- **Conformidade é obrigação de quem entrega por último.** Escreva no prompt do último estágio:
  *"corrija toda violação que achar — isso é obrigação sua, não área de outro"*.
- Ordem dentro do papel: **AUDITA primeiro, REFINA depois**. Se refinar antes, ele investe esforço
  bonito em cima de conteúdo errado.
- Dê ao auditor **acesso à fonte primária**, não só ao relatório do executor. Foi assim que ele
  pegou um arquivo que o executor tinha listado sem abrir (infográfico de terceiro com marca
  d'água de Instagram alheio, prestes a ir pro site de um cliente) e uma frase do cliente que o
  executor perdeu.
- ⚠️ **Nunca confie no autorrelato do executor** — nesta medição ele declarou "sem dificuldades"
  com 3 violações, listou como "foto de produto" 5 arquivos que não abriu, reportou um `logo.jpg`
  que não existia na pasta, e mediu `bytes_antes` da pasta inteira em vez do arquivo. Peça sempre
  campos de **negativa** no schema (`nao_aplicado`, `duvidas`, `pedido_perdido`) e verifique-os.
- Isso é o mesmo princípio do crítico externo, aplicado dentro da própria pipeline: quem fez não é
  quem confere. Ver [[feedback_subagent_critico_padrao]] e [[project_sites_empresas_ouro]] (nota não incluída).


## 🔴 Subagente NÃO serve para trabalho LONGO e sequencial (medido 18/08)
Disparei 2 subagentes Sonnet para processamento pesado (demucs + curvas sobre 50 min de vídeo).
Os dois **encerraram o turno "esperando o background"** — o que termina a execução deles — e a cada
notificação **relançavam trabalho duplicado**: um chegou a rodar um segundo demucs disputando a mesma
GPU, outro subiu um ffmpeg concorrente gravando nos mesmos arquivos de saída. Um deles ainda tratou
minha mensagem de coordenação como possível prompt injection e parou pedindo confirmação (comportamento
correto de segurança dele, mas caro no contexto).
**Custo real: ~660k tokens somados**, para chegar onde eu já estava — o trabalho útil que eles
entregaram foi a **validação em recorte de 60s** (essa sim valiosa: foi ela que descobriu que o demucs
só salva com `--mp3`).
**Regra:** subagente para **fan-out curto e paralelo** (N tarefas independentes de poucos minutos) e
para **validação em amostra**. Trabalho longo, sequencial e com estado em disco → **fazer direto**,
encadeando num único script de background.

## ✅ Codex como terceira voz — o melhor retorno da sessão (18/08)
Pedido pelo autor quando meu detector estagnou. Em uma rodada derrubou **3 erros estruturais** que eu
não enxergava: (1) score escalar único para fenômenos de assinatura diferente; (2) uma feature-sem-tese
como eixo principal; (3) penalidade que expulsava justamente a categoria que o cliente tinha pedido.
Deu desenho concreto (4 cabeças por categoria, features acústicas, detector de risada) que sobreviveu
ao teste com o humano. **Reforça a regra: crítico é de outra linhagem, e o momento certo de chamar é
quando eu já tenho medição mas continuo iterando no mesmo eixo.**

## 2026-09-02 — roteia por TIPO DE PERGUNTA, nao por "quem e mais inteligente"
A/B controlado Codex (`gpt-5.6-sol`) x agy (`gemini-3.8-flash-high`), 6 rodadas, verificacao objetiva (nunca o relato deles). Qualidade **empatou em todas** — codigo, imagem estatica e video. O que separa nao e QI:

- **MENSURAVEL** (extrair, contar, cronometrar, conferir spec, editar codigo) -> **Codex**. Ele nao percebe midia: ele **instrumenta** (cv2/scipy/ffmpeg silencedetect, scripts no disco). Por isso o custo dele quase nao move com tarefa mais dura (+13% de token), e por isso prompt curto ja serve.
- **JULGAMENTO PERCEPTIVO** (a mocao convence, o corte respira, o audio ta sujo) -> **agy**, e **prompt longo e REQUISITO, nao opcional** — prompt preguicoso ali nao da resposta fraca, da resposta errada com cara de certa. Detalhe e numeros: [[feedback_gemini_prompt_preguicoso]].
- **Economia** favorece o agy 3,4x (R$35 x R$120/mes) desde que a cota aguente — variavel ainda nao medida.

🔄 **Corrige a regra antiga "multimodal -> Gemini sempre"**: pra **imagem estatica** o Codex leu um painel 8/8 em **5,8s** contra 165,8s do agy. O fosso do Gemini nao e "multimodal" — e **mocao, audio e video longo**.
🪦 **`Codex > agy pra codigo` fica em SUSPENSO, nao revogado:** no round de edicao o agy editou de verdade, corrigiu a armadilha e **relatou a verdade** — o episodio de fabricacao de 25/06 nao se reproduziu. Base de evidencia agora 1x1, n=1 dos dois lados. Reavaliar exige teste maior, nao inferencia.


## 🪞 O subagente descreve o ambiente DELE, não o meu — e afirma com confiança (22/09/2026)

Mandei um subagente Sonnet auditar notas velhas. Ele voltou com um achado sólido, e no meio dele a frase: *"o próprio sistema informa hoje rodar em **Sonnet 5** (ver system-reminder deste ambiente)"* — usada como **prova** de que a política de modelo do autor estava desatualizada. A sessão era **Opus 5**. Ele leu o próprio system prompt e o descreveu como sendo o meu.

**Why:** cada subagente tem system prompt, modelo, cwd, data de corte e ferramentas **próprios**. Quando ele relata "o ambiente", relata o dele — mas escreve como fato do sistema, e o relatório chega limpo, sem marca de origem. O veredito final estava certo pelo motivo errado, que é o jeito mais difícil de pegar: se eu concordo com a conclusão, não re-audito a prova.

**How to apply:**
- **Fato sobre o AMBIENTE vindo de subagente é sempre suspeito** — modelo em uso, diretório, ferramenta disponível, versão, data. Confiro na minha própria sessão antes de repassar. Fato sobre **arquivo/conteúdo** (o que está escrito, onde, quando) é o que eles medem bem.
- No briefing, **pedir caminho e citação literal** (já é a regra do `pesquisador`) — isso não impede a projeção, mas faz ela aparecer: alegação de ambiente vem sem caminho.
- Gêmeo do [[feedback_subagent_critico_padrao]]: lá o risco é o briefing fazer ele CONFIRMAR; aqui é ele confundir a própria casa com a minha.

📐 **E o número que justifica delegar, medido nesta sessão:** 6 frentes de pesquisa processaram **1,28 milhão de tokens** e devolveram **~52k** pro meu contexto — razão de **24:1**. É a mesma ordem do exemplo oficial da Anthropic (6.100 lidos → 420 devolvidos). O isolamento é real e mensurável; o custo é 3–10× mais tokens no total e perda de contexto em cada costura. Ver [[reference_janela_contexto]] e [[feedback_custo_mora_no_contexto]].
