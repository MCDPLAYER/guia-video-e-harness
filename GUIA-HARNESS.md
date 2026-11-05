# Guia de harness — lições principais

Resumo de como o autor montou e manteve o próprio setup de Claude Code em 2026: o `CLAUDE.md`, as memórias,
os subagentes, a orquestração com outros LLMs (Codex, Gemini), o crítico e o debug. Cada seção aponta o
arquivo com o detalhe.

**Como ler:** igual ao `GUIA-VIDEO.md`. **(preferência do autor)** = gosto ou rotina dele, troque pela sua;
o resto é medido. ⏳ = perecível (modelo, preço, cota, versão de CLI). Os nomes de modelo são os de 2026 e
envelhecem rápido; o que importa é o método de medir.

---

## 1. A camada sempre-ligada — `harness/CLAUDE.md`, `notas/harness/feedback_boas_praticas_setup.md`

- **Só `CLAUDE.md` + `MEMORY.md` entram em toda sessão.** É o único texto pago sempre: mantenha enxuto.
  Teste por linha: "remover isto me faria errar?". Excesso faz o modelo ignorar TODAS as regras. Numa poda,
  a cinta caiu 33% (7.139 → 4.791 caracteres).
- **Estrutura que funcionou:** cinta curta → rotas IF→THEN (gatilho nas palavras do usuário → skill →
  ponteiro) → princípios transversais → instruções de compactação. O COMO mora na skill/nota, nunca na cinta.
  O `harness/CLAUDE.md` deste repo é o modelo anonimizado: copie a estrutura, não as rotas.
- **Doc obrigatório tem teto** (60 linhas por carta de cliente, 150 por skill: números do autor). Linha nova
  reescreve uma antiga em vez de somar.
- **Limites medidos ⏳:** `MEMORY.md` trunca calado em 25 KB / 200 linhas; description de skill até 1.536
  caracteres e a lista inteira cabe em ~1% da janela (estourou, as menos usadas viram só o nome); `: ` solto
  numa description quebra o YAML em silêncio.
- **Memória guarda o que não muda sozinho.** Número, preço, versão e status levam validade ("⏳ reconferir")
  e lista viva mora em UM arquivo.

## 2. Manter regras sem virar "superstição sofisticada"

- **Poda por FUNÇÃO, não por tamanho** (ativo / gatilho + contraexemplo / rubrica / arquiva / remove).
  Regra "X não" preserva o gatilho. Negação só fica se barra um default real do modelo ou um erro que já
  aconteceu. Mudança no setup: congela ~2 semanas, muda uma coisa só, changelog de 1 linha.
  → `notas/harness/feedback_boas_praticas_setup.md`
- **Não funda notas-irmãs para encolher o índice:** a description é o vetor de busca; fundir causa regressão
  silenciosa de recall. → idem
- **Regra que briga com o instinto do modelo vira campo fixo** (4 de 4 modelos descreveram foto de banco de
  imagem como "a loja"). Mas campo fixo também vira licença (10 de 11 peças passaram a carregar o assunto no
  campo errado): faça o teste do campo coberto. → `feedback_regra_vira_campo_fixo.md`
- **Doc de regras que só adiciona polui canvas limitado** (cards de 3 → 7+ blocos; as regras explicavam ~2
  dos 7). Teto é orçamento com ordem de corte. Classifique cada regra nova: adiciona / remove / substitui /
  limita / prioriza / condiciona. → `feedback_regras_que_so_adicionam.md`
- **Não generalize regra de um cliente ou eixo para outro.** POC de ferramenta amplia o menu, não elege
  vencedor. → `feedback_nao_generalizar_regras.md`
- **Audite a adoção antes de escrever do zero:** a peça aprovada usou o kit existente 13×; a reprovada, 1×,
  e reimplementou 6 símbolos (um deles fez a peça tremer por 7 renders). → `feedback_usar_o_que_ja_existe.md`

## 3. Subagentes, custo e delegação — `harness/agents/`, `feedback_custo_mora_no_contexto.md`

- **O custo mora no CONTEXTO, não no output:** numa sessão de US$ 669 (preço de API), output foi 5,8%;
  cache write 63,8%, cache read 30,4%. As alavancas são menos turnos e modelo mais barato.
- **Régua de delegação:** `T > custo_do_agente ÷ (corpus − relatório)` ≈ 4 turnos, com piso de ~21,5k tokens
  + ~1,7k por tool call. Contexto SUJO (ruído que você leria e jogaria fora) justifica delegar mesmo abaixo da
  régua. Delegar a edição de um arquivo que você já leu não tira o arquivo do contexto (o diff volta).
- **Subagente = PAPEL, com modelo fixado no arquivo:** `scout` (barato, read-only, devolve fato + caminho),
  `worker` (escopo fechado, escreve "SUPUS:" em vez de perguntar), `smart-worker` (reproduz o passo literal,
  mede, propaga), `pesquisador` (cita regra LITERAL, nunca parafraseia). Cada um com formato de entrega fixo.
- **Modelo explícito sempre:** omitir herda o modelo do loop principal (um workflow rodou 15 agentes no
  modelo mais caro, 2,1M tokens). Papel novo só fica chamável na sessão seguinte.
  → `feedback_workflow_modelo_opus.md`
- **Subagente descreve o ambiente DELE como se fosse o seu** ("rodando Sonnet" numa sessão Opus): fato de
  ambiente vindo de subagente é suspeito; fato de conteúdo, com caminho e citação, é confiável. Isolamento
  medido: 1,28M tokens lidos → ~52k devolvidos (24:1). → `feedback_orquestracao_multi_llm.md`
- **Subagente serve para fan-out curto, não para trabalho longo e sequencial** (~660k tokens gastos com
  processos duplicados disputando a GPU). → idem
- (preferência do autor) modelo mais forte só com OK dele; delegar por padrão e auditar.

## 4. Orquestrar outros LLMs — `skills/pipeline-multi-llm/`, `feedback_orquestracao_multi_llm.md`

- **Pesquisa bem-feita ≠ conclusão certa; o gate é EXECUTAR.** Uma pesquisa com fontes oficiais concluiu
  que um serviço rodava no plano grátis; a chamada real devolveu `402` depois do app pronto. Quanto mais cara
  a decisão, mais cedo vem a menor execução que prova.
- **Crítico nunca é da mesma linhagem** (Claude criticando Claude é groupthink); executor pode ser qualquer um.
- **Crítico é detector, não redator** (26/08: 14 achados, 11 procediam, 0 de 14 direções de conserto
  aproveitadas). Como redator a partir de briefing funciona com **teto numérico** (sem teto: 40% acima;
  com "máx 2.800 caracteres": 2.798) e guarda-corpo factual.
- **Promova modelo crítico só por teste cego com defeitos plantados** (recall, precisão, severidade, custo),
  por classe de risco.
- **Saída indexada se valida com script contra a fonte**, sem reler (ex.: 29 bordas de corte conferidas
  contra o SRT com tolerância de 60 ms antes de ~20 min de encode).
- **Tier não depende de "raciocínio":** raciocínio fechado e verificável empatou até num modelo local
  pequeno; a diferença aparece no agêntico (tool calls). Classifique a subtarefa por ferramenta/loop,
  horizonte, gosto e volume sem verificação.
- **Em pipeline bruto → refino, o último estágio AUDITA antes de refinar** (o executor declarou "sem
  dificuldades" com 3 violações): peça campos de negativa (`nao_aplicado`, `duvidas`).
- **Roteie pelo tipo de pergunta:** o mensurável vai para quem instrumenta (Codex leu um painel 8/8 em 5,8 s
  contra 165,8 s); julgamento perceptivo de vídeo/áudio vai para o modelo multimodal, com prompt longo.
- **Prompt curto faz o Gemini AFIRMAR errado:** mesmo effort, 795 contra 5.878 tokens de raciocínio, e o
  resultado passou de errado a 5/5. Enumere os itens e aceite "não consegui apurar".
  → `feedback_gemini_prompt_preguicoso.md`
- **CLIs externas no Windows ⏳:** prompt sempre em arquivo via stdin (`-p` estoura ~32 KB); `CODEX_HOME`
  dedicado (o config do app desktop derrubava 100% das chamadas); path longo, não 8.3. → `SKILL.md`
- **Logs de execução moram no workspace, nunca dentro da pasta da skill.** Neste export, prompts e saídas
  com texto de cliente estavam dentro da skill e vazaram junto (a pasta foi apagada). Grave também qual
  modelo/effort rodou.
- (preferência do autor) a alocação de cota entre Codex e Gemini; Codex reservado para ele.

## 5. O crítico e o revisor às cegas — `feedback_subagent_critico_padrao.md`, `skills/peca/ref/REVISOR.md`

- **O briefing é a variável do crítico, não o modelo.** Em 99 achados, só 1 era ajuste fino; foram 349 verbos
  de corte contra 6 de acréscimo; e o cliente recusou 5 peças por serem rasas contra 0 por serem arriscadas.
  Peça a versão MELHOR, não a mais segura.
- **Pelo menos um crítico com ZERO contexto:** brief que explica a intenção faz o defeito parecer intenção.
- **O crítico lê trecho a trecho:** contradição distante passa ("7 dias" × "90 dias"); coerência global é
  trabalho do orquestrador. Peça reescrita depois da crítica volta para o crítico.
- **Formato de revisor às cegas que funcionou numa peça real** (de um log apagado): três blocos (TEXTO DA
  PEÇA · FICHA DE FATOS com origem e data de cada fato · VITRINE das últimas peças) e 5 perguntas fixas:
  1. bloqueio de fato (frase sem apoio na ficha; "nenhum" é resposta válida);
  2. bloqueio de entendimento (3 segundos: o que vende e de quem é?);
  3. quem ganha, o cliente final ou só a marca?;
  4. repete alguma peça da vitrine?;
  5. versão MELHOR de alguma frase.
  Deixar "nenhum" escrito impede o revisor de inventar problema para justificar a rodada.
- **Auditoria de documento longo por LLM externo** (outro log apagado, e-book de 42 páginas): aponte o
  arquivo em vez de colar; avise que duplicações podem ser ruído de extração; peça **tabela de recálculo
  de TODOS os números** (24 conferidos: 1 erro de subtração + 1 conclusão errada por consequência), página +
  trecho literal em cada achado, separação entre erro comprovável e recomendação, "requer verificação" para
  fato externo, e "5 pontos fortes que não podem se perder". A regra "não invente fato externo" reduz mas
  não zera: trate as lacunas de domínio como hipótese.
- (preferência do autor) rodar o crítico por padrão, sem perguntar.

## 6. Debug e verificação — `notas/harness/feedback_fix_root_cause.md`

- **Diagnostique antes de prescrever.** "Faça X, não Y" é contenção de dano; pergunte por que saiu Y.
- **Verificação que não detecta a classe de falha é teatro:** reproduza o passo LITERAL do usuário;
  `py_compile` não pega import faltando (importe o módulo); meça no alvo real; teste que consagra o bug,
  "concorrência" numa conexão só e benchmark que mede a ordem (3,1× falso por cache) são teatro. Pergunte
  "falharia se eu quebrasse de propósito?".
- **Consertar ≠ propagar:** depois de um fix, procure o nome literal em todas as camadas (memória, vault,
  skills, código, o próprio doc canônico).
- **Caixa-preta:** A/B contra algo que JÁ passou, uma variável por vez. Lentidão: relógio por fase antes de
  teorizar (a teoria errou por 3 ordens de grandeza). Fama de "instável": linha do tempo desde o último
  sucesso REAL — o rótulo "quebra recorrente" escondeu uma queda contínua de 14 dias (ver `GUIA-VIDEO.md` §4).

## 7. Contexto, modelo e hooks

- **Janela anunciada ≠ janela útil ⏳:** o que quebra primeiro não é achar fato, é SEGUIR
  REGRA dada antes (começa a quebrar por volta de ~4.000 tokens) — e depois de compactar a violação vai de 0%
  a 30–59%, por isso decisão importante vai para arquivo, nunca para o resumo; pergunta no FIM do prompt rende até +30%; `autoCompactWindow`
  tem que ser número. → `notas/harness/reference_janela_contexto.md`
- **Modelo/effort ⏳:** o effort máximo custou +73% sobre o anterior com ganho zero em código e contexto
  longo; o modelo do subagente se escolhe por medição. "Sempre o modelo mais recente" é preferência do autor.
  → `notas/harness/feedback_modelo_tier_policy.md`
- **Hooks:** só alguns eventos injetam contexto (SessionStart, UserPromptSubmit, Pre/PostToolUse, Stop,
  SubagentStop); captura de memória é julgamento, então fica manual e o Claude OFERECE; matcher por ferramenta
  não vê a mesma edição feita via shell. Mecânica de hook se confere na doc oficial.
  → `notas/harness/reference_claude_code_hooks.md`

## 8. Capturar aprendizado — `skills/capturar/`, `skills/digest-mensal/`

- **O endereço vem antes do texto:** classifique por "o que acontece se eu errar de novo?" (lei, fato,
  pedido, marca, risco, ferramenta, método, gosto). Gosto vira uma linha de observação com a fala literal;
  regra nasce de repetição (3 observações, ou 2 em clientes diferentes).
- Dois filtros antes de gravar: "eu re-derivaria isso do zero?" e "um profissional faria sem o doc?".
- **Revisão mensal "cega" não é "sem dado":** os dados estão nos transcripts e nas datas. Âncoras antes do
  fan-out (baseline + diff, schema único, checklist de eixos validada por crítico de outra linhagem); captura
  em paralelo, escrita serial com backup primeiro; logue o que foi amostrado ou descartado.
- **Regra de ouro da matriz de roteamento:** o band-aid mora na skill/script; só a causa-raiz sobe para a
  cinta. → `skills/digest-mensal/checklist-mae.md`
- (preferência do autor) cadência mensal, caderno no Obsidian, carta por cliente.

## 9. Preferências do autor (não copie sem pensar)

Pensar em inglês e responder em PT-BR; contar o EFEITO, não a mecânica; arquivar só com "sim" explícito;
limpeza só quando ele aciona; postura autônoma (ir fazendo sem pedir permissão a cada passo); latência de
5–30 s de CLI externa aceitável; as rotas, pastas e clientes do `harness/CLAUDE.md`.
