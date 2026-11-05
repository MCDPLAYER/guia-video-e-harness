# Checklist-mãe — eixos de captura do digest mensal

> A "1 chance". Cada átomo capturado preenche `schema.json` e carimba um EIXO. Errar pro lado da **recall**: na dúvida, capture com `confianca:baixa` — a precisão vem na correlação/poda, não aqui. Eixo que volta vazio = suspeito → o completeness-critic confere. Os exemplos `(ex: …)` são reais do acervo do autor (anonimizados), pra calibrar o que conta; troque pelos seus.

## A. Decisões & políticas — o mais valioso (causa-raiz)
- Decisões novas que mudam como trabalhar (ex: search default → AUTO; Gemini single-key).
- Decisões **revertidas/supersedidas** → `supersede` + corrigir a fonte velha (ex: fal reprovado; multi-projeto Gemini = ToS).
- Regras/preferências de trabalho novas (viram `feedback`).
- Trade-offs **rejeitados** e por quê — "não fizemos X porque Y" vale tanto quanto "fizemos".

## B. Aprendizados técnicos & gotchas
- Bug → fix, mas só a **causa-raiz** (o band-aid mora na skill/script/config, vá lá).
- Gotcha de ferramenta (ex: PS1 encoding/BOM; ffmpeg DLL Windows; mecânica de hook).
- Receita/comando que funcionou (com o comando literal).
- **O que falhou e por quê** — igualmente valioso, costuma escapar.

## C. Ferramentas & stack — mudança de capacidade
- Instalado / atualizado / aposentado (ex: pwsh 7.6.2; agy 1.0.7; Gemini CLI sunset).
- Versão / quota / limite novo.
- MCP / skill nova ou alterada.
- **Credencial/key nova → RAG `_credenciais/`** (nunca em arquivo local externo).

## D. Projetos & clientes
- Projeto/cliente NOVO (ex: `<cliente-G>`; uma marca/estúdio novo do autor).
- Marco/entrega por projeto.
- Mudança de status (pausado/retomado/concluído).
- Aprendizado por cliente (mood, paleta, CTA, compliance) — nunca misturar mood entre clientes.

## E. Mídia & assets produzidos
- Peças geradas (imagem/vídeo/áudio) — onde estão? **embedadas no índice de mídia?**
- Refs / moodboards / clean-plates novos.
- O que ficou no **Desktop não triado**.

## F. Conhecimento (vault / RAG)
- Nota / doc canônico novo ou alterado.
- Conhecimento que **deveria** ter virado nota e não virou (lacuna).

## G. Sistema / infra
- RAG: índices, serviço indexador (fila, cobertura, `status.py`), storage, frescor.
- Disco de trabalho / Drive: reorg, espaço, estrutura.
- Automação / cron / n8n / script novo.
- Pipeline (porta-http, comissões, clip-search…).

## H. Pendências & obrigações futuras — TODOs datados
- O que ficou prometido pra depois, com **data absoluta**.
- Aprovação aguardando (ex: brief `<cliente-ótica>` v2 pós-Codex).
- "A melhorar" reconhecido (ex: HERO de carrossel `<cliente-farmácia>` fraco).

## I. Gastos & quotas — risco real
- Crédito gasto (fal / Omni / API paga).
- 429 / billing / quota batida.
- Risco de ToS / suspensão.

## J. Higiene meta — a própria memória/instruções
- Memórias criadas no mês — qualidade, dedup, description que fisga recall?
- Notas-irmãs a fundir.
- Bloat de CLAUDE.md / MEMORY.md (teste "remover causa erro?").
- Fontes stale detectadas.

## K. Portabilidade / dependência de máquina (C:/Windows) — ativar em contexto de migração de PC
- Programa instalado (ffmpeg/+nvenc, python, node, yt-dlp, whisper, demucs, git); versão de runtime (pwsh); PATH/env var.
- **venv Python** (paths absolutos → recriar, NÃO copiar); **Task Scheduler / HKCU Run** (ex.: o serviço indexador); serviço de startup (porta-http).
- Config em `<pasta-do-usuario>\.claude` (skills, settings, MCP, hooks); auth de CLI (agy, codex — re-login); **letra de drive** (script com letra de unidade fixa no código quebra se o disco remontar com outra letra).
- Cada átomo K → linha do `SETUP-NOVA-MAQUINA.md` com **COMO recriar**. Gotchas jun/26: **a unidade do Google Drive fica ancorada/cacheada no disco do sistema** (não é "de graça em espaço"); **não usar WSL** pra esse stack (paths Windows-nativos).

---

## Matriz de roteamento (camada-alvo por tipo de átomo)

| Átomo | Vai pra |
|---|---|
| Regra sempre-ligada, curta, dispara sozinha | **CLAUDE.md** (cinta) — só se passa "remover causa erro?" |
| Domínio / workflow recorrente | **skill** |
| Fato/decisão/contexto durável e recuperável | **memória** (`<tipo>_<slug>.md` + ponteiro em MEMORY.md) |
| Conhecimento extenso / cliente | **nota no vault** (RAG) |
| Fato/lei/pedido/marca de cliente | **`CARTA.md`** do cliente (substitui a linha que já fala disso) + changelog do `DECISOES.md` |
| Gosto / "saiu feio" | **`<pasta-do-vault>\_caderno\CADERNO.md`** (1 linha de observação) |
| Asset / mídia | **disco de trabalho** no lugar certo + embed no índice |
| Credencial | **RAG `_credenciais/`** |
| Pendência datada | **memória `project`** + Task Scheduler local |
| Nada durável | **descarta** — mas **loga o descarte** (no silent caps) |

> Regra de ouro do roteamento: o fix pontual mora na skill/script/config; só a **causa-raiz/regra geral** sobe pra memória ou cinta. Band-aid não vira cânone.
