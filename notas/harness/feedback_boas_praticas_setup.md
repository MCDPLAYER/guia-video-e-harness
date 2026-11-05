---
name: feedback_boas_praticas_setup
description: "Estou seguindo as boas práticas do Claude Code / mantendo meu setup certo? Fundamentos (camada sempre-ligada enxuta, poda>incha, offload, captura que o Claude oferece) + o MÉTODO de manutenção: como podar memória/skill sem virar 'superstição sofisticada' — poda por FUNÇÃO e não tamanho, regra 'X não' preserva o gatilho, negação só se barra default do modelo, checklist mental em 3 níveis, gosto vira sinal (eval fraco: taxa de retrabalho / erro recorrente), governança de mudança (congela ciclo + changelog). Angústia 'meu trabalho é simples, por que engasgo?'."
metadata: 
  node_type: memory
  type: feedback
  modified: 2026-09-23T14:24:25.191Z
---

Revisão das boas práticas do Claude Code em **2026-06-23** contra a doc oficial (`code.claude.com/docs/en/best-practices`). Veredito: setup ~80% alinhado. Os **fundamentos certos** (o que sustenta o resto):

1. **Camada sempre-ligada (CLAUDE.md + MEMORY.md) ENXUTA.** Carrega TODO logon = único custo de toda sessão. Teste por linha: *"remover isto me faria errar?"* Se não, corta. Bloat faz o modelo IGNORAR regras (failure pattern oficial "over-specified CLAUDE.md").
2. **Poda > incha.** Regra ignorada ≈ ruído demais, não regra de menos. Auto-evolução CORTA, não acumula roteamento.
3. **Offload.** Domínio/detalhe/tutorial → skill/nota sob demanda. Na cinta só roteamento (IF→THEN curto) + princípios transversais.
4. **Captura proativa.** Não há hook de fim-de-sessão (SessionEnd/PreCompact/PostCompact não agem; Stop é por-turno) — e captura é JULGAMENTO, não ação mecânica → o Claude OFERECE `/capturar`, não depende do usuário lembrar. Ver [[reference_claude_code_hooks]].
5. **Verificação/evidência.** Dar prova (teste/screenshot/assistir o vídeo), não afirmar "pronto". Já forte: [[feedback_video_julga_mocao_nao_frame]], [[feedback_verificar_ui_navegador]] (nota não incluída).
6. **Crítico independente.** Review de outra linhagem/contexto fresco. Já forte: [[feedback_orquestracao_multi_llm]].
7. **Explore-first + specificity gate.** Busca no RAG antes de agir; pergunto a 1 variável de risco (cliente exato). Já forte.

**Já feito em 23/06** (passos 1-2 da revisão): cinta podada **33%** (7139→4791 chars), Rotas+Regras fundidas (duplicação morta), justificativa longa cortada; captura movida pra "o Claude oferece"; auto-evolução reescrita ("a cinta PODA, não incha"). Backup do CLAUDE.md antigo em `<pasta-temp>\2026-06-23\CLAUDE.md.bak`.

**Why:** a camada sempre-ligada é a alavanca de maior ROI do setup — é o único texto pago em TODA sessão, e bloat ali degrada adesão a TODAS as regras de uma vez. Vale mais podar 1 linha aqui que afiar 10 numa skill.

**How to apply:** ✅ **Digest de junho FEITO em 2026-07-01** (pendência cumprida): captura completa das 167 sessões via Codex (volume) + Sonnet/Haiku (fan-out híbrido quando a cota Codex torrou no meio), correlação ancorada no RAG, propagação por camadas. **Achado do run:** o vault estava **bem-mantido** — a maior parte do aprendizado de junho já fora registrada na hora, então o digest achou poucas correções de fato; o valor real foi o `SETUP-NOVA-MAQUINA` (migração jul→ago), a política de modelo e os gotchas de ferramenta (skill `pipeline-multi-llm`). A skill `digest-mensal` foi refinada no run (eixo K de portabilidade, executor Codex `reasoning=low`, fan-out híbrido Codex→Sonnet/Haiku). Poda profunda de notas-irmãs (109 notas) fica pra próxima passada. Ver [[project_segundo_cerebro_obsidian]] (nota não incluída) e o digest em `…/_arquivo/digests/junho-2026/`.

---

## Método de manutenção do sistema (2026-07-03, contraponto Codex gpt-5.5)

Origem: o autor questionou se "log morto" em skill/memória polui, se checklist mental serve, e a angústia "meu trabalho é mais simples que codebase de ponta, por que engasgo?". Rodou contraponto Codex (furou pontos meus e dele). 6 doutrinas:

**1. Poda por FUNÇÃO, não por tamanho.** Antes de deletar, classifica cada item: **ativo** (regra viva) · **gatilho+contraexemplo** (o "X não" reescrito com a condição) · **vira rubrica/exemplo** (referência de qualidade, não regra) · **arquiva** (histórico real → corpo da nota/RAG, sai da camada quente) · **remove** (preferência morta / negação decorativa / autópsia datada). Só "remove" é deleção; os outros 4 são *função errada de camada*, não lixo. Chesterton's fence: histórico datado às vezes é **anticorpo** (evita um erro esquecido) — deletar às cegas = o erro volta com cara nova.

**2. Regra "X não" preserva o GATILHO.** "Não usar verde" pelado é cicatriz sem memória do corte → o agente re-deriva o erro. Formato: *gatilho + contraexemplo mínimo + ponteiro RAG*. **Doutrina do autor (confirmada):** concisão na camada quente + **log completo no RAG** = mais seguro que deletar. Não some com o detalhe — relocaliza. Skill enxuta E detalhe consultável.

**3. Negação sobrevive só se barra um default REAL.** Teste: a negação (a) barra um default forte do **modelo** (arquétipo — farmácia→cruz-verde; o modelo conhece arquétipo, não o teu cliente), (b) barra um erro que **já aconteceu** contigo, ou (c) é preferência morta. Só (a)+(b) ficam; (c) é ruído + priming. Medir pelo default do modelo, não pelo mundo real do cliente.

**4. Checklist mental — 3 níveis.** Mecânico (contou caractere? bateu dimensão? CTA é texto?) → checklist inline que força *escrever o número/citar o elemento*. Rubrica comparativa → "compara com estes 5 bons e 5 ruins, aponta divergências". Subjetivo (on-brand? converte?) → crítico externo de outra linhagem ([[feedback_subagent_critico_padrao]]). Inline só serve pro mecânico; evidência sozinha ainda vira teatro. **O autor adota:** checklist + reasoning/effort alto sobe o piso.

**5. Gosto vira SINAL (eval fraco).** Não tem unit test pra "tá bom", mas ignorar o eval fraco É o gap de orquestração real (não é "só gosto, relaxa"). Medir barato: **taxa de retrabalho por cliente**, **log de erro recorrente** (o que repete vira regra/rubrica — ex. [[feedback_cliente_farmacia_cta_duas_unidades]] (nota não incluída), mood trocado entre óticas), **banco aprovadas/reprovadas** (= golden-set das rubricas do item 4), **qual skill gerou mais retrabalho**. Enquadramento do autor: pra autonomia a base tem que ser sólida o bastante pra cobrir as nuances — a subjetividade do bom gosto é o caro pra LLM fechar em loop.

**6. Governança de mudança (o gargalo real).** Tunar skill+RAG+rota+prompt JUNTOS toda semana = impossível saber o que ajudou; sem baseline, poda vira "superstição sofisticada". Anti-veneno: **congela ~2 semanas, muda UMA coisa, changelog de 1 linha** (motivo / hipótese / o que esperava / o que deu). É a cinta ("correção RECORRENTE = atualizo") aplicada ao próprio autor.

**Why:** as 6 atacam a mesma doença — entropia (o sistema cresce mais rápido que a poda) + manutenção sem baseline vira achismo. O autor NÃO é leigo em orquestração (RAG semântico, multi-LLM tiered, Flow headless = mais infra que a média); faltava método de poda + eval fraco + baseline.

**How to apply:** poda das notas → passa cada uma pelos baldes do item 1 (decide função, não "encurta"). Regra nova → item 2 (gatilho+ponteiro). Próxima leva de mudanças no setup → liga o changelog (item 6). Princípios 1-2 embutidos no ritual `/capturar`. Ver [[feedback_rotas_camada_sempre_ligada]] (nota não incluída), [[feedback_orquestracao_multi_llm]], [[feedback_nao_generalizar_regras]].

---

## 7. NÃO fundir notas-irmãs pra encolher o índice (2026-07-10, contraponto Codex gpt-5.6-sol)

**Unidade de armazenamento ≠ unidade de recuperação.** A `description` de cada nota É o vetor do `search.py`. Fundir N notas colapsa N vetores em 1: o índice encolhe ~150 B por linha, mas a superfície de busca encolhe junto — e a **regressão de recall é SILENCIOSA** (você descobre 3 meses depois, quando a memória certa deixa de aparecer).

**O número mata o plano antes da filosofia:** fundir os 9 candidatos óbvios (4 `<cliente-farmácia>` + 3 `<cliente-ótica>` + óticas + Remotion) rende ~1,35 KB. Não bastava nem pra bater a meta. Pagaria dano permanente e o alerta continuaria.

**Dois anti-padrões nomeados:**
- **Fusão por homonímia** (2 bugs de Remotion sem relação, só compartilham a ferramenta) — dilui consultas indiretas ("funciona no preview e quebra no render").
- **Fusão que apaga a fronteira** (`<cliente-G>` verde + `<cliente-H>` preto/dourado numa nota só, duas óticas concorrentes) — pior que perder recall: gera **recall CONTAMINADO**. O corpo dizendo "nunca misturar" não salva; o modelo erra colando o atributo na entidade vizinha. Regra negativa de alto custo (LGPD, compliance, "nunca misturar") **fica atômica**.

**Limite real ≠ meta do hook.** O changelog do Claude Code: *"MEMORY.md index now truncates at 25KB as well as 200 lines"*. Em 10/07: 19,3 KB / 132 linhas = **77% dos bytes, 66% das linhas**; folga pra ~39 notas. A meta de 17,1 KB do hook é **política de crescimento (68% do teto)**, não limite técnico. **Confira o teto antes de obedecer o hook.** 🪧 **Correção 23/09 (v2.1.280): o aviso de 17,1 KB é do PRÓPRIO Claude Code** (70% do teto de leitura), não hook do autor — o único hook dele segue `sync-skills.sh`. O aviso não trunca nada.

**🆕 28/07 — o passo (1) tem uma alavanca melhor que "encurtar gancho": AGRUPAR CLIENTE numa linha.**
Um cliente com N subtemas (`<cliente-farmácia>` 8 notas, `<cliente-ótica>` 7) vira UMA linha com os N ponteiros
(`· [rótulo](arquivo.md) gancho ·`): rende nas **duas** dimensões do teto (bytes E linhas) e não
perde link nenhum. Rendeu 20,7→19,0 KB e 143→138 linhas numa passada, com os 128 links conferidos
por diff antes/depois — **faça esse diff, é o único jeito de provar que a poda foi sem perda**.
⚠️ **Mas isto NÃO vale pra entidades CONCORRENTES.** Agrupei as 3 óticas numa linha — cores lado
a lado — e tive que desfazer: é a "fusão que apaga a fronteira" de cima, só que na camada do
índice, que é PIOR porque o `MEMORY.md` está sempre no contexto. **Teste antes de agrupar: os
itens são subtemas de UM dono (agrupa) ou irmãos que disputam o mesmo slot mental (não agrupa)?**

**🆕 28/07 — arquivar ficou BARATO, e isso muda a ordem acima.** Desde 27/07 as notas de memória
entram no coletor de texto do [[project_indexer_sqlite]] (nota não incluída; 160 notas, 342 vetores Gemini + 135 NIM).
Provado de ponta a ponta: busquei um tema de nota **já arquivada** sem saber o nome dela e ela veio
em **1º lugar** (`cos=0,774`). ⇒ Arquivar custa só o recall ESPONTÂNEO, não a localização.
⚠️ Mas **arquivar rende pouco em bytes**: os 10 projetos dormentes do índice somam **1 KB**. Se a
motivação for tamanho, agrupar > arquivar; arquive por estar MORTO, não pra ganhar espaço.

**Se um dia encostar nos 25 KB, a ordem é:** (1) encurtar rótulo/gancho sem tirar ponteiro; (2) camada `MEMORY-ARCHIVE.md` — tira do índice só concluído/pausado/troubleshooting raro, **mantém a nota e sua `description` indexadas**; (3) stubs de recuperação (nota fica com frontmatter + ponteiro pra seção consolidada); (4) multivetor por nota (campo `retrieval:` com uma frase por átomo). Fundir corpo é o ÚLTIMO recurso, e só depois do multivetor existir.

**Invariante (o teste de aceite de qualquer poda):** *todo átomo capaz de mudar uma decisão futura precisa de uma rota independente de recuperação* — linha no `MEMORY.md`, OU vetor próprio (entidade + gatilho + ação/exceção), OU entrada multivetor. **Não** basta contar "121 ponteiros continuam lá": guarde 2-3 perguntas naturais por memória crítica e exija o acerto no top-3 **antes e depois** da poda — sem trazer regra da entidade errada.

## ⚰️ O `/digest-mensal` morreu — e levou junto o dono da PODA (o autor, 04/09/2026)

> *"o digest mensal morreu por falta de necessidade prática, agora que o 'capturar' existe, ficou sem
> essa necessidade de mapear as seções pra procurar aprendizados"*

**Why — e o dado já estava escrito aqui.** A entrada de 01/07 registra que o digest de junho *"achou
poucas correções de fato, porque o vault estava bem-mantido — a maior parte do aprendizado de junho já
fora registrada na hora"*. O `/capturar` no fim de cada sessão tornou a varredura retroativa redundante.

🔴 **Mas o digest era o único dono da PODA**, e a mesma entrada de 01/07 termina com *"poda profunda de
notas-irmãs (109 notas) fica pra próxima passada"*. **Essa passada nunca veio.** É por isso que a
`MEMORY.md` incomoda todo dia: ela cresce por captura e não encolhe por ninguém.

**How to apply:** a poda passa a ser o **passo 4-bis do `/capturar`** — incremental, do tamanho do que
entrou na sessão, com a régua *"se eu tirar isto do índice, eu ainda saberia que preciso procurar?"*.
📌 **E a calibragem medida em 04/09: poda rende pouco.** 8 linhas mais pesadas do índice deram **372
bytes (1,7%)**, contra os 2-3 KB estimados — porque **21% dos bytes são sintaxe de link** e o resto,
em sua maioria, é anticorpo. **O crescimento é aritmética de 146 notas, não gordura** ⇒ quando a poda
não bastar, o passo é **arquivar** (`MEMORY-ARCHIVE.md`), não podar mais fino.

## 🚦 O índice não precisa virar router — o ROUTER JÁ EXISTE, e é a lista de skills (piloto medido 04/09/2026)

**O pedido do autor:** *"tem que ter um jeito de limpar esse topo, nem que seja a memory virando só um
router pra notas e skills e o rag"*. A intuição estava certa; o alvo, não.

**Os três orçamentos, medidos:**

| Camada | Tamanho | Teto | Roteamento |
|---|---|---|---|
| `CLAUDE.md` (cinta) | 9,2 KB | sem teto documentado | passivo |
| `MEMORY.md` (índice) | ~20 KB | 🔴 **25 KB / 200 linhas, truncagem silenciosa** | passivo |
| **`description` das 49 skills** | **23,4 KB** | fora do teto de 25 KB, **mas tem teto PRÓPRIO** (ver 🪧 abaixo) | 🟢 **ativo — o modelo lê e decide invocar** |

🔴 **As descriptions das skills já custam MAIS que a `MEMORY.md` inteira**, já são carregadas em toda
sessão e **não competem pelo teto de 25 KB**. O router que o autor queria construir já existe, já está
pago, e é o único dos três em que o roteamento é **ativo**.

🪧 **Correção 23/09/2026 — "não trunca" estava ERRADO** (lido no binário v2.1.280 + a lista real desta sessão):
- **Teto por skill: 1.536 caracteres** de description (`skillListingMaxDescChars`); o que passa é cortado.
- **Teto da lista inteira: 1% da janela** (`skillListingBudgetFraction` 0.01). Estourou → modo "priority": as skills **MAIS USADAS** (contagem de uso com meia-vida de 7 dias) ficam com a description e **o resto vira só o NOME, sem gatilho nenhum**. Rico fica mais rico: skill esquecida some do roteamento.
- ⏳ **Medido 23/09:** 129 skills na lista, ~30 mil caracteres (~7,5k tokens) por sessão. **78 foram só pelo nome**, quase todas de plugin (`small-business` ~40, `adobe` ~13, `anthropic-skills` ~23). **As 27 locais do autor passaram inteiras**, porque ele usa.
- ✂️ **Poda feita em 23/09 (decisão do autor):** plugins `small-business`/`adobe`/`cowork` apagados no claude.ai; **14 skills locais arquivadas** (27 → 13) em `skills/_arquivadas-2026-09-23/`, com cópia no RAG e `_LEIA.md` do porquê de cada uma. Critério = uso REAL contado nos transcripts (skill chamada ou SKILL.md lido), não impressão. Método pra repetir: contar por sessão em `~/.claude/projects/**/*.jsonl` + o `skillUsage` do `~/.claude.json`.
- 💥 **Description com `: ` solto (ex.: "quando aparecer: Remotion") quebra o YAML e o Claude Code troca a description pelo TÍTULO do arquivo — calado.** Pegou `video-remotion` (desde 21/09) e `gflow` (editada 23/09); consertado pondo a description entre aspas. **Editou description → valida o YAML.**

⇒ **Regra que sai daqui: cluster de gatilhos que sempre dispara JUNTO, no mesmo ponto do fluxo, vira
SKILL.** A `description` é o gatilho (orçamento próprio, com o teto acima); o corpo só carrega quando invocado;
**as notas continuam sendo o porquê e o histórico** — a skill aponta pra elas, não as substitui.

### O piloto, com número

Cluster escolhido: os 5 gates de produção de peça (anti-eco · profundidade · card se explica sozinho ·
o que pode afirmar · QC pós-render) → skill **`gates-da-peca`**.

| | bytes |
|---|---|
| saiu do índice (5 linhas → ponteiros) | **−945** (4,5% da `MEMORY.md`) |
| entrou como `description` da skill | +763, **em orçamento próprio** (fora dos 25 KB, com teto próprio — ver 🪧) |
| corpo da skill | 11.011, **só carrega quando invocada** |

📊 **Comparação que decide o método:** a poda tradicional das 8 linhas mais pesadas rendeu **372 bytes
(1,7%)**; o piloto rendeu **945 (4,5%) com 5 linhas** — 2,5× mais, migrando menos da metade das linhas.

**Por que funciona melhor que podar:** poda tenta cortar anticorpo (que é a maior parte do peso e não
deve sair); a migração **muda o anticorpo de orçamento**, sem perder nenhum.

### ⚠️ O trade-off, honesto

Skill que eu não invoco = anticorpo perdido — furei a `image-gpt` em 06/08 e 31/08. **A diferença é
que falha de skill é AUDITÁVEL** ("abriu antes do primeiro prompt? sim/não" — foi assim que o autor me
pegou nas duas vezes), enquanto memória diluída falha em silêncio e ninguém percebe.

📌 **O que NÃO migra:** gatilho sem momento de disparo. *"Nunca misturar os dois usuários da mesma conta"* não tem
skill pra invocar — é sempre. Isso fica no índice por natureza.

**Próximos clusters candidatos** (mesma régua: dispara junto, no mesmo ponto do fluxo): orquestração
multi-LLM · busca/RAG · mídia e vídeo. **Replicar um por vez e medir** — se o recall piorar, reverte
um cluster, não cento e quarenta.

