---
name: digest-mensal
description: >
  Ritual MENSAL industrializado de consolidação — varre as sessões/trabalho do mês inteiro (N
  sessões, eu chego "cego"), captura átomos de aprendizado COM EVIDÊNCIA via fan-out de agentes,
  CORRELACIONA usando os índices/RAG do usuário (a infra cara que ele montou), e propaga pras camadas
  certas (CLAUDE.md, memórias, skills, notas, vault) + deixa o disco de trabalho nos conformes com o RAG.
  Use quando o usuário pedir "faz o digest do mês", "consolida junho/julho", "ritual de consolidação",
  "correlaciona o mês", "poda a memória do mês", "deixa o RAG/disco nos conformes", ou na virada de
  mês (~dia 1, rotina do autor). NÃO confundir com /capturar (= fim de UMA sessão, contexto vivo); este é
  multi-sessão, cego, com fan-out + correlação semântica. Ver nota feedback_boas_praticas_setup.
---

# /digest-mensal — consolidação mensal industrializada

> **Preferência do autor (troque pelos seus):** ele roda isto todo mês, perto do dia 1, sobre o setup dele:
> vault Obsidian com RAG próprio (`_rag-tools`), um serviço indexador agendado, um índice de mídia, e o Codex
> como crítico de outra linhagem. O reaproveitável é o mecanismo: baseline + diff, schema único, checklist de
> eixos validada por um crítico antes do fan-out, captura read-only em paralelo, correlação ancorada no índice
> que já existe, escrita serial com backup. Placeholders: `<pasta-do-vault>` = raiz do vault;
> `<disco-de-trabalho>` = onde moram projetos e mídia; `<pasta-temp>` = área de rascunho.

Captura e propaga o aprendizado de ~1 mês sem perder nada relevante, sabendo que vou correlacionar **depois**. Irmão de escala do `/capturar`: aquele é 1 sessão com contexto vivo; aqui são N sessões e eu chego **cego**.

## A virada de chave: cego ≠ sem dado

Não adivinho o mês — o dado existe e é recuperável, só não cabe num contexto:
- **Transcripts** das sessões: `<pasta-do-usuario>\.claude\projects\<slug-do-projeto>\*.jsonl` (1 por sessão). Ferramentas: `list_sessions` + `search_session_transcripts`.
- **Filesystem** (`<disco-de-trabalho>`, `<pasta-do-drive>`, Desktop): `LastWriteTime` no mês delata todo asset/nota/skill mexido.
- **Memórias** novas: datas + `originSessionId`.
- **Baseline**: o digest anterior em `<pasta-do-vault>/_arquivo/digests/`.

Logo, a "1 chance" não é adivinhar conteúdo — é **cobrir os eixos e as fontes**. O agente lê a fonte real; eu só não posso esquecer um eixo.

## As 3 decisões-âncora (ANTES de qualquer agente sair)

O gargalo não é capturar, é **correlacionar**. Trave os três:
1. **Baseline + diff** — fotografe 01/<mês> (memórias, CLAUDE.md, skills, índice RAG, árvore do `<disco-de-trabalho>`). Capture só o **delta**. Sem isso, re-processa o universo e afoga a correlação.
2. **Schema único** (`schema.json`) — todo átomo, de todo agente, no mesmo formato. Schema = correlação vira *merge*, não re-leitura.
3. **Checklist-mãe** (`checklist-mae.md`) — a taxonomia de eixos. Falta um eixo = aquilo some. **Passe a checklist por um crítico de outra linhagem (Codex) ANTES do fan-out** — é 1 chance, vale o adversarial.

## As fases (read-only diverge → write converge)

Regra-mãe: **nunca misturar leitura com escrita**. Captura é paralela e read-only; aplicação é serial.

1. **Localizar + baseline** — snapshot 01/<mês>; `list_sessions` do mês; `LastWriteTime` no `<disco-de-trabalho>` e no `<pasta-do-drive>`.
2. **Travar schema + checklist** → Codex valida a checklist.
3. **Fan-out de captura** — `fanout.workflow.js` (não incluído): 1 agente por cluster de sessões/fonte, lê e extrai átomos schema-bound. **Read-only.** Erra pro lado da recall (captura generosa; `confianca:baixa` marca o incerto, não descarta).
4. **Correlação via RAG** (seção abaixo) — dedup vs baseline, cluster semântico, detectar stale/supersessão.
5. **Roteamento** — cada átomo → `camada_alvo` + `acao`. Decisão, ainda sem escrever. Codex poda o over-capture.
6. **Aplicação** — writes com **backup primeiro** (`<pasta-temp>\aprendizados-<mês>\`), na ordem: notas/memórias/cartas/caderno → skills → CLAUDE.md por último (e só o que passa "remover causa erro?").
7. **Reconciliação disco↔RAG** (seção abaixo).
8. **Verificação adversarial** — recall test, links, cobertura (`status.py`), contagem.
9. **Digest humano** (`DIGEST-<mês>.md` no vault) + **novo baseline** pro próximo ciclo.

## Correlação ancorada no RAG (o ROI — usar o que foi caro montar)

A correlação **não** é manual nem por LLM cega. É ancorada nos índices que já existem (caros e completos — é a alavanca de assertividade do usuário):
- **Cluster semântico** — embedo os átomos (JSONL) num índice temporário com a MESMA stack (`_rag-tools`) e agrupo por vizinhança: "a mesma decisão dita em 3 sessões" colapsa num átomo só.
- **Dedup vs memória institucional** — pra cada átomo, `search.py "<claim>" --top 5` contra vault+memória. Já existe nota? → `acao:atualizar` (nunca nota-irmã).
- **Detecção de stale** — o `search.py` acha a nota VELHA que o átomo novo contradiz → preenche `supersede` → corrige na FONTE (anti-padrão "fonte stale me empurrou pro erro").
- Motores (no setup do autor): TEXTO `<pasta-do-vault>/_rag-tools/search.py` · MÍDIA `<pasta-do-projeto>/_clip-search/search.py --index <X>`.

## Reconciliação disco de trabalho ↔ RAG (deixar nos conformes)

O RAG é a fonte de verdade do "o que é buscável". Ancore no mapa canônico do seu RAG (no setup do autor, `<pasta-do-vault>/RAG-ARQUITETURA-DUAL-STACK.md`, não incluído, seção "o que falta pra 100%"):
- **Órfãos** — arquivo no `<disco-de-trabalho>` fora de todo índice → liste (quem indexa é o serviço indexador; não rode build na mão).
- **Fantasmas** — entrada no índice cujo arquivo sumiu → liste; a poda é a frente 6 do `/limpar`, acionada pelo usuário.
- **Desktop/_temp** — liste o que ficou e quanto; quem aciona o `/limpar` é o usuário.

## Gates de segurança (inquebráveis)
- Captura (fases 1-4) é **100% read-only**. Nada de escrita/move/delete até a fase 6.
- **Backup ANTES** de qualquer write (preferência forte do autor: ele tem trauma de perder arquivo).
- Delete só de **dup confirmada no filesystem** (índice sozinho NÃO autoriza), via **Lixeira**, **nunca a única cópia**. `_conferir` e áreas do user intocáveis. Destrutivo isolado do move.
- **No silent caps** — logue o que foi amostrado/dropado/não-varrido. Corte silencioso lê como "cobri tudo".
- Crítico/poda/validação = **Codex, nunca outro Claude** (política do autor: crítico de outra linhagem; skill `pipeline-multi-llm`); o Gemini só assiste vídeo.
- Política grande/incerta → Codex antes de canonizar.

## Arquivos desta skill
- `checklist-mae.md` — os 11 eixos (A-K; o K só em migração de PC) + sub-itens + matriz de roteamento. A "1 chance".
- `schema.json` — o schema de captura (correlacionável por máquina). Não incluído neste repo.
- `fanout.workflow.js` — Workflow script de captura paralela schema-bound (não incluído neste repo). Disparo: `Workflow({scriptPath:"<pasta-de-skills>\\digest-mensal\\fanout.workflow.js", args:{mes:"2026-06", fontes:[...]}})`. **v0 — refinar no dia com a lista real de sessões.**

## Pegadinha + como conferir
- ⚠️ **Não pule o baseline.** Sem o diff 01/<mês> você re-captura nota velha e a correlação afoga. Conferir: nº de átomos pós-dedup deve ser « nº bruto.
- ⚠️ **Schema drift mata a correlação.** Agentes DEVEM emitir via StructuredOutput (validação no tool-call). Conferir: todo JSONL valida contra `schema.json` antes da fase 4.
- ⚠️ **Workflow não tem meu contexto** — os prompts dos agentes são self-contained e carregam o schema inline.
- ⚠️ **Anti-alucinação** (Gemini alucina em volume): átomo sem `evidencia` (quote+path) entra com `confianca:baixa` e NÃO é escrito sem verificação.
- **Verificação final (rode e espere):** pra cada memória nova, `search.py "<pergunta plausível>" --top 5` tem que **fisgar** a nota (senão a description não presta — reescreva). Confirme pelo `status.py` que a nota nova foi indexada (não rode build na mão). Wikilinks `[[]]` resolvem (obsidian CLI unresolved). Só então liste o que sobrou no Desktop/_temp e quanto (quem aciona o `/limpar` é o usuário).

## Calibração (run jun/2026)
- **Pré-limpar transcripts** antes do fan-out: extrair só turnos user/assistant dos `.jsonl` — corta ruído e custo brutalmente.
- **Executor:** Codex `-c model_reasoning_effort="low"` sem perder qualidade na extração → **fan-out híbrido**: Codex até esgotar a cota → **Sonnet (lotes grandes) + Haiku (menores)** via Workflow, 1 agente/lote. **agy = NÃO** pro volume (frágil headless). ⏳ medido no run de jun/2026; reconferir: hoje o CLAUDE.md manda volume/mídia pro agy. Detalhe em `pipeline-multi-llm`.
- **Correlação:** convergência real = mesmo `cluster_key` em **sessões DISTINTAS** (não N átomos da mesma sessão longa). Vault maduro → dedup **alto** (maioria `JA-EXISTE`; "já coberto" ≠ redundante — pode ser update). **O reindex bate a cota FREE diária do Gemini:** a indexação roda no serviço indexador (no setup do autor, acompanhe com `python <pasta-do-indexador>\status.py`). Não force o pago sem OK.
- **Achado meta:** vault bem-mantido → o digest achou **poucas correções** (aprendizado já registrado na hora). **Digest "magro em correções" = sinal de saúde, não de falha.**

---
Relacionado: `capturar` (1 sessão) · `consolidate-memory` (poda genérica) · `pipeline-multi-llm` (tiers de delegação) · busca no vault (regra do CLAUDE.md + `<pasta-do-vault>/RAG-ARQUITETURA-DUAL-STACK.md`) · [[project_segundo_cerebro_obsidian]] (nota não incluída) · [[reference_recall_so_memory]] (nota não incluída) · [[reference_gemini_rotacao_storage]] (nota não incluída) · nota `feedback_boas_praticas_setup` (os fundamentos; em `notas/harness/`).
