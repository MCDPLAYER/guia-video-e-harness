# Destinos do arquivamento — Fase 2 do `/limpar`

Movido do `SKILL.md` (Fase 2). A regra de fundo continua lá: **só mover, nunca no mesmo passo
que deleta, sempre a partir da lista CLASSIFICADA**. Aqui ficam a tabela de destinos e as
pegadinhas de nome/convenção por cliente.

> Rotina do autor: as pastas abaixo são do setup dele (vault no Drive para texto, disco de mídia
> para mídia) e viraram placeholders. Troque pelas suas; as pegadinhas valem para qualquer setup.

| o quê | destino |
|---|---|
| copy / prompt / decisão / transcrição (TEXTO) | `<pasta-do-drive>\obsidian\<pasta-de-clientes>\<Cliente>\_producao\` |
| leva de posts (o pacote do dia) | `<pasta-do-drive>\obsidian\<pasta-de-clientes>\_pacote-posts-<data>.md` |
| card / arte final (MÍDIA) | ver tabela de convenções abaixo |
| vídeo / reel / saída de IA | `<pasta-de-trabalho>\<cliente>\3-videos\` |
| não sei | holding |

### 📌 Texto que vai pro RAG tem que virar `.md`
O índice **só lê `.md`**. Um `.txt` copiado pro vault entra **morto** pro recall — está lá e a
busca não acha. Converta (com frontmatter `tipo/cliente/data`) e, na nota, **aponte pro path
no disco de mídia onde a mídia ficou**. A nota é o registro; o disco é o arquivo.

### 📌 Os nomes de cliente NÃO batem entre os drives
Confira o destino real — não presuma simetria. Exemplo genérico dos padrões que existiam no setup
do autor (caixa, grafia e árvore mudam de um drive pro outro e de um cliente pro outro):

| cliente | RAG (Drive) | mídia (disco) |
|---|---|---|
| `<cliente-A>` (padrão mais comum, vários clientes) | `ClienteA` | `cliente-a\<pasta-de-peças>\DD-MM-tema\card.png` |
| `<cliente-B>` (padrão próprio, por série) | `ClienteB` | `CLIENTE-B\<rede-social>\<série>\post-NN_...\CARD.png` |
| grupo de clientes do mesmo setor (ex.: 4 óticas) | um nome por marca (`<ClienteX>`, `<ClienteY>`…; mais um nome legado) | `<SETOR>\<Marca>\` — **confira o destino real**; o padrão flat `posts\<marca>-YYYY-MM-DD-tema.png` foi medido em 3 das 4 marcas (conferido em 30/09 também na 4ª; a pasta `posts\` de uma marca nova estava vazia, sem padrão ainda) |

Confira a contagem depois de mover. Não fechou → **para** e investiga.

### 📌 Card de um cliente tem um SEGUNDO destino: a grade do site (14/08/2026)

No setup do autor, o card do `<cliente-sindicato>`, depois de movido pro
`<pasta-de-trabalho>\<cliente>\<pasta-de-peças>\`, é **copiado também** pra

```
<pasta-do-drive>\<pasta-do-cliente>-site\instagram\
```

Essa pasta alimenta o bloco "No Instagram" da home do site institucional — uma tarefa agendada
(`<cliente>-Site-Instagram`, de 6 em 6 h) pega as **6 mais recentes** e republica sozinha.

- **É cópia, não movimentação.** A pasta de peças continua sendo o arquivo canônico.
- **Não precisa curar nem renomear na hora:** se a pasta estiver desatualizada, basta jogar o
  arquivo lá. O nome vira a descrição da imagem, então prefira `tema-do-card.png` a
  `card.png`/`IMG_2026.png` — mas nome ruim não quebra nada, só empobrece o `alt`.
- **Tirar do site = tirar o arquivo da pasta.**
- Conferir se rodou: `<pasta-do-cliente>\_site-institucional\_capturas\sync-instagram.log`.

⚠️ **Antes de copiar, confira nome e cargo de quem aparece na peça contra a `CARTA.md` do
cliente.** Peça com pessoa vai pra home do cliente, e cargo errado ali é erro publicado: em 14/08
a peça de um integrante da diretoria entrou na grade identificando-o como *"Diretor"* do sindicato
— a agência já tinha corrigido para **Suplente de Diretoria**.
