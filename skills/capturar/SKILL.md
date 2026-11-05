---
name: capturar
description: >
  Ritual DELIBERADO de captura de aprendizado da sessão (segundo cérebro do usuário). Use quando o
  usuário digitar "/capturar" ou pedir pra "fechar a sessão", "salva o que aprendemos", "registra na
  memória o que rolou", "consolida o aprendizado", ao encerrar um trabalho substancial. É o ritual
  de FIM de sessão, rodado de propósito — NÃO é auto-trigger de toda menção a "memória"/"nota".
  Decide PRA ONDE o aprendizado vai: carta do cliente, skill da ferramenta, nota de memória ou
  caderno de observações.
---

# /capturar — pra onde vai o que aprendemos

> **Preferência do autor (troque pelos seus destinos):** no setup dele cada cliente tem um `CARTA.md`
> (modelo em `skills/peca/ref/CARTA-MODELO.md`), o gosto vai pra um caderno num vault Obsidian revisado
> por uma tarefa agendada semanal, e a busca é um RAG próprio sobre o vault. O reaproveitável é o mecanismo:
> classificar pelo custo de errar de novo, gosto vira observação até repetir, substituir a linha em vez de
> somar, teto por doc. Placeholders: `<pasta-do-vault>` = raiz do vault; `<pasta-de-memoria>` = memórias do Claude Code.

> **Teto: 150 linhas.** Lição nova sobre captura REESCREVE uma linha daqui.
> Toda linha que entra em doc de leitura obrigatória é paga em toda peça, pra sempre. Por isso a
> primeira decisão é o endereço, e a maioria das coisas vai pro caderno.

A maioria das sessões não gera memória nova. "Nada durável pra gravar" é uma resposta boa.

## 1. Classifique: o que acontece se eu errar isso de novo?

| Se errar de novo… | É | Vai pra | Como |
|---|---|---|---|
| multa, processo, conselho de classe, política de plataforma | **LEI** | `CARTA.md` do cliente, seção Verdade | agora, com o trecho literal da norma |
| a peça afirma algo falso (número, preço, prazo, spec, contato) | **FATO** | `CARTA.md` ou a fonte canônica que ela aponta | agora, com a fonte |
| o cliente ou a agência pediu e eu descumpro | **PEDIDO** | `CARTA.md`, seção Pedido | agora, com data e a fala literal |
| logo, embalagem, cor ou nome da marca saem errados | **MARCA** | `CARTA.md`, seção Marca e assets | agora, com o caminho do arquivo |
| gasta dinheiro, publica, apaga, vaza credencial | **RISCO** | `CLAUDE.md` ou a nota de segurança que já existe | agora |
| a ferramenta quebra, ou devolve errado calada | **FERRAMENTA** | a skill ou o `ref\` daquela ferramenta | agora, com a medição (data, n) |
| eu trabalho do jeito errado com o usuário (rota, delegação, custo) | **MÉTODO** | nota de memória `feedback_*` (§3) | agora |
| a peça fica menos bonita, menos viva, "parecida", "sem graça" | **GOSTO** | `<pasta-do-vault>\_caderno\CADERNO.md` | uma linha de observação |
| não sei dizer | **GOSTO** | caderno | uma linha de observação |

**Os seis primeiros são especificação; o sétimo é método; o resto é gosto.** Especificação e método eu gravo
na hora, no lugar certo. Gosto eu anoto e espero repetir.

## 2. Gosto vai pro caderno como OBSERVAÇÃO

Uma linha, no fim do arquivo, neste formato:

```
- AAAA-MM-DD · <cliente ou eixo> · <o que aconteceu, em fato> · usuário: "<fala literal>" · <caminho da peça ou da sessão>
```

- Escrevo **o que aconteceu**, do jeito que aconteceu: "o card da água saiu com fundo liso e ele trocou pela
  versão com bancada". A generalização fica pra revisão semanal, que enxerga as repetições.
- A fala do usuário entra literal. É ela que a revisão vai comparar.
- Um caso é uma observação. **Regra nasce de repetição:** três observações do mesmo mecanismo, ou duas em
  clientes diferentes, ou duas derrotas no voto do usuário em trinta dias.
- A revisão semanal (no setup do autor, a tarefa agendada `caderno-revisao-semanal`) agrupa, escreve a proposta em positivo e
  diz qual linha ela substitui. Quem promove é o usuário, aprovando a proposta.

## 3. Especificação e método: grave no lugar, substituindo

**Antes de escrever, ache a linha que já fala disso** (Grep pelo termo na carta, na skill ou na memória;
sem termo literal, a busca semântica do vault; no setup do autor, `python "<pasta-do-vault>/_rag-tools/search.py" "<tema>" --top 5`). Existe: reescrevo
aquela linha. Não existe: entra linha nova, e eu confiro o teto.

| Doc | Teto | Quando enche |
|---|---|---|
| `CARTA.md` de cliente | 60 linhas e 9 KB | sai a linha mais fraca pra `<pasta-do-cliente>\CARTA-SOBRAS.md` |
| skill (`SKILL.md`) | 150 linhas | detalhe desce pra `ref\`; a skill fica com o caminho |
| `MEMORY.md` | 25 KB e 200 linhas (truncagem silenciosa) | ponteiro que não dispara mais vai pro `MEMORY-ARCHIVE.md` |
| `CLAUDE.md` | só rota | detalhe mora na skill ou na nota |

**Redação.** A linha diz o que fazer: "a headline se entende sozinha". "Nunca" e "proibido" ficam pra LEI,
PEDIDO e RISCO, e carregam o motivo junto. Uma linha por assunto: três variações do mesmo tema viram uma.

**Decisão de cliente** também ganha uma linha datada no changelog do `DECISOES.md` dele (o histórico que o
usuário e a agência consultam). A carta é o que a produção lê.

**Número que muda sozinho** (preço, quota, versão, status de espera, veredito sobre ferramenta): vai pro RAG,
ou entra com validade explícita (`⏳ reconferir a partir de <mês>`).

**Dois filtros antes de gravar método ou ferramenta:**
- *Eu re-derivaria isso do zero numa sessão futura?* Se sim, não gravo.
- *Um profissional competente faria assim sem o doc?* Se sim, não gravo.

### Nota de memória nova (só quando não existe nota do tema)

Arquivo: `<pasta-de-memoria>/<tipo>_<slug-kebab>.md`. Tipos: `user` · `feedback` (como
trabalhar, com o porquê) · `project` (trabalho em curso) · `reference` (ferramenta, fato).

```markdown
---
name: <slug-kebab>
description: "quando recall: <as perguntas REAIS que fariam esta nota ser útil, com os termos de busca: ferramenta, cliente, erro, número>"
metadata:
  node_type: memory
  type: feedback | project | reference | user
---
```

- A `description` é gatilho de busca: perguntas, com as palavras que eu digitaria pra achar a nota.
- Corpo de `feedback`/`project`: o fato + **Why:** + **How to apply:**.
- Nota operacional (CLI, pipeline): seção `## Pegadinha + como conferir`, com um passo de verificação
  concreto ("rode X, espere Y").
- Link liberal com `[[outra-nota]]`.
- Existe nota do tema: atualizo nela. Ao podar, decido a função de cada linha (ativa · gatilho · exemplo ·
  arquivo · remove); histórico datado às vezes é o anticorpo. Método: [[feedback_boas_praticas_setup]].

### Ponteiro na MEMORY.md (obrigatório pra nota nova)

O auto-recall lê só a `MEMORY.md`; nota sem ponteiro fica invisível. Uma linha na seção certa:
`- [Título](arquivo.md) — <gancho: quando isso é útil>`

## 4. Poda do tamanho do que entrou

Depois de mexer na `MEMORY.md`:
1. Meço: `wc -c` e `wc -l`. Teto do harness: 25 KB e 200 linhas.
2. Olho só as linhas que toquei e pergunto: **se eu tirar isto do índice, eu ainda saberia que preciso
   procurar?** O que mora num doc que a rota já abre sai do índice (fica o ponteiro). O que eu não saberia
   procurar fica: é anticorpo. Átomos da mesma família se fundem.
3. Antes de cortar, Grep no destino confirmando que o detalhe está lá, e backup
   `MEMORY.md.bak-poda-<data>`.
4. A poda rende pouco (medido 04/09: 1,7%): 21% do índice é sintaxe de link. Quando não basta, o passo
   seguinte é **arquivar o ponteiro** no `MEMORY-ARCHIVE.md`. O critério é "ainda dispara recall útil?";
   data de modificação não serve (cliente que produz todo dia fica meses sem tocar na nota).

## 5. Política grande ou incerta

Regra ampla, decisão estrutural, mudança de rota: passa pelo Codex antes de virar cânone (no setup do autor, pelo
wrapper `codex-run.ps1`, não incluído).
Linhagem diferente da minha enxerga o que eu confirmo sozinho.

## 6. Feche

Feche dizendo o que gravei e onde, o que foi pro caderno e o que descartei, com o porquê.

---

Histórico e números: `<pasta-do-vault>\_ferramentas\DESBUROCRATIZACAO-2026-09-30.md` (não incluído).
