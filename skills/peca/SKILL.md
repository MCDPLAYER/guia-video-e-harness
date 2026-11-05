---
name: peca
description: >
  Produção de peça ESTÁTICA de cliente (card, carrossel, story, capa, ad) e de LOTE de posts:
  "faz a mídia do <cliente-farmácia>", "post de quarta da <cliente-entidade-lojista>", "adianta os posts de amanhã", "monta o
  pacote de segunda". Método curto: carta do cliente + ficha de fatos + vitrine + um revisor às
  cegas. É a rota única de peça de cliente desde 30/09/2026 (image-gpt, gates-da-peca,
  criativos-rede-social e o protocolo do crítico foram arquivados). Vídeo por código →
  motion-studio. Operar o Flow pela UI → gflow.
---

# peca — peça estática de cliente

> **Teto: 150 linhas.** Lição nova REESCREVE uma linha daqui. Não entra linha sem sair outra.
> Medido no teste cego do `<cliente-farmácia>` (29-30/09/2026): por este caminho uma peça leva 8–11 min; pelo anterior
> levava 23–30 min, e o voto do autor fechou 2×2 (empate = fica o caminho mais barato). A rota anterior carregava ~77 mil tokens de regra antes
> do primeiro prompt; esta carrega esta página, a carta do cliente e uma imagem.

## O que entra (e só isso)

1. **CARTA** — `<pasta-do-cliente>\CARTA.md`, uma página. É a especificação do cliente: o que é
   verdade, o que ele pediu, o que a marca é, o formato, o que já ganhou.
2. **VITRINE** — `<pasta-do-cliente>\_design\VITRINE.jpg`: folha de contato das últimas peças aprovadas. Uma imagem.
3. **FICHA da pauta** — os fatos desta peça, cada um com a fonte (site do cliente, material que ele mandou,
   doc do vault). O que não tem linha na ficha não vai pra arte nem pra legenda.
4. **MECÂNICA da ferramenta** — `ref\chatgpt-images.md` (card, carrossel, tipográfico) ou `ref\foto-flow.md`
   (foto realista). Abro na hora de escrever o prompt.

`DECISOES.md`, `IDENTIDADE` e brand-profile são histórico. Abro quando a carta aponta pra lá ou quando o
trabalho é mudar o SISTEMA do cliente (série nova, identidade, cliente novo). Pra produzir peça, não.

## Caminho

1. **Cliente exato e pauta.** Grade em `project_agenda_posts_recorrentes` (nota de memória não incluída); se a carta aponta arquivo de slots
   (a do `<cliente-G>` aponta), a pauta do dia sai dele: data comemorativa vai no slot que a grade marcou, não no post
   mais perto (06/10: o Dia das Crianças era de 12/10). "Ótica" sem nome (há mais de uma ótica na carteira): pergunto qual.
2. **Leio a carta e olho a vitrine.**
3. **Monto a ficha.** Fato que falta: `scout` busca e volta com trecho + caminho. Material que o cliente
   mandou pedindo post autoriza a peça; o número dentro dela ainda precisa de fonte.
4. **Escolho UMA ideia e UMA família de cena**, diferentes das três últimas da vitrine.
5. **Escrevo** texto da arte, prompt e legenda.
6. **Monto a pasta** (abaixo).
7. **Revisor às cegas**, uma vez.
8. **Entrego:** caminho da pasta, a ideia em uma frase, o que ficou diferente da vitrine, e as dúvidas que
   deixei sinalizadas.

Dúvida minha não encolhe a peça: produzo inteira e sinalizo ao lado. Só pergunto antes quando a resposta
muda a peça (qual cliente, qual produto, gasto, publicação, imagem de pessoa real).

## Gramática (o que faz a peça ganhar)

- **Uma ideia, dita na headline.** Quem passa o dedo entende em três segundos o que é e de quem é, sem ler
  o resto.
- **Cena encorpada.** O mundo do produto: matéria, luz, objeto real, gente, lugar. Venceu nos quatro votos
  cegos, dos dois métodos ("mais encorpado e vivo", "mais bonito e vivo"); fundo liso com muito vazio perdeu ("muito vago").
- **O que é real, entra real.** Pote, embalagem, logo, foto do fundador, tela do sistema: anexo o arquivo e
  o prompt diz onde ele fica e o que acontece em volta.
- **Do lado de quem compra,** no vocabulário dele. A peça responde "e eu com isso?".
- **Texto que se conta nos dedos:** headline de até ~10 palavras, apoio de até ~15, no máximo três blocos.
  A frase se lê de uma vez.
- **Cada peça muda pelo menos duas coisas** em relação às três anteriores: família de cena, enquadramento e
  escala, campo de cor ou luz, tratamento do texto. A marca fica no que a carta fixa; o resto se mexe.
- **A legenda continua o card** (conta o que a arte não coube) e fecha com a assinatura da carta.

### Famílias de cena (escolho uma antes de escrever)

bancada rica do mundo do produto · macro da matéria ou do ingrediente · gente usando, no lugar real ·
produto em estúdio com contexto e luz dirigida · tipográfico com um objeto âncora · documento ou prova
(nota, tela, selo, print) · mapa ou passo a passo (carrossel) · bastidor (quem faz, como é feito)

## O que não sai daqui

- Afirmação, número, preço, prazo ou promoção sem linha na ficha.
- Logo, rótulo ou embalagem redesenhados quando existe o arquivo real.
- Contato, endereço ou nome da marca digitados de memória: copio da carta.
- Pessoa real identificável sem o cliente ter mandado a foto.

## Pasta de entrega

> Preferência do autor: a estrutura de pastas abaixo é a rotina dele com a agência. O método é: só o final na
> pasta que vai pro cliente, base/anexo fora, um TXT com prompt + legenda + origem de cada frase.

`<pasta-de-entrega>\<AAAA-MM-DD>\NN-CLIENTE-slug\` (no setup do autor, uma pasta no Desktop)
- só o que vai pro cliente/ADS (peças finais numeradas). Base e anexo de referência do ChatGPT ficam FORA,
  em `<pasta-de-entrega>\<data>\_bases\NN-…` (autor, 23/10: "elas confundem o pessoal do ADS");
- `PROMPT-E-LEGENDA.txt`: o prompt (colar inteiro), a legenda, e "de onde saiu cada frase" (frase → linha
  da ficha). Um caminho só; versão alternativa vai em `_EXTRAS\`;
- na raiz do dia, `ORDEM-DO-DIA.txt` (uma linha por pasta).

Duas sessões no mesmo dia: sufixo na pasta do dia, pra uma não sobrescrever a outra. Cópia do texto no
vault: `<pasta-de-clientes>\_pacote-posts-<data>.md`, só a tabela do dia e, por peça, ideia + texto da arte + legenda
+ prompt. Notas de produção não entram.

## Revisor (um, depois, às cegas)

Brief fixo em `ref\REVISOR.md`. Roda no Codex (`codex-run.ps1`); se o Codex estiver sem cota, agy.
Ele recebe o texto da peça, a ficha e a vitrine, sem saber como a peça foi feita. **Bloqueia só em dois
casos:** afirmação que a ficha não sustenta, ou não entendeu o que a peça vende. O resto volta como
sugestão, e eu decido. Em lote, um revisor pro lote inteiro.

## Lote (vários clientes no mesmo dia)

1. Eu monto a tabela do dia: cliente · pauta · ideia em uma frase · família de cena. É aqui que se evita
   dois clientes com a mesma ideia no mesmo dia.
2. Um `worker` (Sonnet) por cliente, todos na mesma mensagem. Ele para em 30 turnos (8 de 11 pararam em 01/10 e terminaram com um SendMessage "continue"): o brief manda gravar a pasta cedo, e eu retomo quem parou. Brief fechado: caminho da carta, da vitrine
   e da mecânica + a linha da tabela + a pasta de entrega. Ele monta a ficha, escreve e monta a pasta.
3. Eu leio só os `PROMPT-E-LEGENDA.txt`, ajusto voz e ideia onde precisar, e rodo um revisor pro lote.
   A folha de contato do lote (uma imagem com tudo) vai em `00-FOLHA-PRA-APROVAR.jpg` na raiz do dia: a
   agência aprova pelo zap com 1 imagem em vez de 10+ (autor, 26/10: "agencia curtiu essa folha de contato").

## O olho: eu gero e olho antes de entregar

Card e carrossel eu mesmo gero no ChatGPT Images, pelo Claude in Chrome (passo a passo em
`ref\chatgpt-images.md`; aperto de mão do navegador primeiro). Abro a imagem em resolução cheia, ponho ao
lado da vitrine e corrijo o prompt pelo que vejo: olhar o resultado vale mais que prever o defeito. A
`GERADA-*.png` vai na pasta do pacote. Chrome fechado ou aperto de mão que falhou = entrego só o pacote e
aviso. Peça aprovada e postada entra na vitrine (`tools\vitrine.py`), com uma legenda curta de por que
ganhou quando o usuário disser.

## Quando o usuário corrige

- **Fato, lei, dinheiro, pedido do cliente ou da agência, asset da marca:** linha na CARTA agora
  (reescrevendo a que já fala disso; carta tem teto de 60 linhas e 9 KB) + uma linha datada no changelog do
  `DECISOES.md` do cliente.
- **Gosto, "saiu feio", "prefiro o outro":** conserto a peça e anoto uma linha no caderno
  (`obsidian\_caderno\CADERNO.md`). Não vira regra aqui. A revisão semanal decide o que sobe.
- **Peça perdeu no voto:** procuro o mecanismo que faltou e escrevo UMA linha em "O que já ganhou aqui"
  na carta. Não volto o método antigo inteiro.

## Mudar o SISTEMA de um cliente

Cliente novo, série nova, identidade nova: aí eu leio o `DECISOES.md` (REGRAS ATIVAS) e a IDENTIDADE,
proponho, o usuário aprova, e o resultado é a carta reescrita + a vitrine. Modelo em `ref\CARTA-MODELO.md`.
- **Logo solta já no nascimento (preferência do autor):** recorto a logo com fundo transparente, em versão claro e escuro, na pasta
  `logo\` do cliente, e a carta aponta pra ela. Foto de perfil (quadrado ou círculo) não se anexa: o modelo
  cola o quadrado como adesivo no canto. Autor (07/10): "deixar a logo orgânica assim como todos os nossos
  clientes fazem - menos a fábrica acho". Exceção mora na carta do cliente.
- **Cor do design = cores da logo, em toda peça.** A variedade gira em modos de campo DENTRO dessas cores,
  mais família, enquadramento e texto. Tirar a cor da marca do campo pra fugir de outro cliente deu
  terracota e marrom no `<cliente-ótica-4>` (autor, 07/10: "as artes sem a cor da logo").

## Se der ruim

Backup de tudo antes da mudança: um zip em `<pasta-de-midia>\_backups\setup-2026-09-30_pre-desburocratizacao.zip`. As skills anteriores
estão arquivadas em `skills\_arquivadas-2026-09-30\` (não incluídas). Método e números: `obsidian\_ferramentas\DESBUROCRATIZACAO-2026-09-30.md`
(nota não incluída).
