---
name: limpar
description: >
  Libera espaço sem perder nada: abre cada arquivo, manda pro lugar certo (TEXTO/CÓDIGO→RAG,
  MÍDIA→disco de mídia/Drive, incerto→holding) e só então apaga o que já está salvo ou é lixo.
  Use quando o usuário digitar "/limpar" ou pedir "limpa meu desktop", "o desktop tá entulhado",
  "arquiva isso aí", "fechei o dia, limpa", "libera espaço no SSD", "limpa a pasta de renders". É UMA
  passada com SETE frentes, nesta ordem: pins novos do Pinterest, Desktop, vídeo do canal já
  publicado (sobe pro Drive), cortes de live do canal secundário de lives (só o corte longo do autor fica),
  parceria musical, sobras de render do motion-studio (<pasta-de-renders>), e por fim arquivo
  truncado/fantasma no indexer. LIMPAR ≠ ORGANIZAR — o sucesso é espaço liberado sem
  perder um byte. Nunca chuta o que é um arquivo: abre (Read/ffprobe/Whisper/Gemini) ou busca
  nos índices; só deleta o que CONFIRMOU (hash, ou a API do Drive) que já existe fora dali.
  QUEM ACIONA É O USUÁRIO — a skill não roda sozinha nem vira tarefa agendada.
---

# /limpar — liberar espaço sem perder nada

O autor despeja o trabalho do dia no Desktop e depois pede pra limpar. Este é o playbook pra
fazer isso **sozinho** — sem perguntar onde salva cada coisa.

> Rotina do autor (preferência do autor): as sete frentes e as pastas abaixo são o setup DELE (Windows,
> SSD de mídia + Google Drive + vault Obsidian com RAG, canal + clientes). Os caminhos viraram
> placeholders e os scripts da skill não estão incluídos. O que se reaproveita é o método: identificar →
> arquivar → gate por hash → relatório; três portas (arquiva / Lixeira / holding); nunca apagar sem prova.

## Uma skill, uma passada, sete frentes

`/limpar` é **um trabalho só**. O autor acumula os jobs e chama quando tem tempo — então
quando ele chama, você faz a **passada inteira** e entrega **um relatório**. Não são sete
comandos, não são sete skills, e você não fica perguntando "quer que eu faça a parceria
também?" a cada etapa. (preferência do autor)

**A ordem não é arbitrária — siga:**

| # | frente | alvo | o que acontece |
|---|---|---|---|
| 1 | **Pinterest** | os boards de `boards.txt` → 4 pastas em `<pasta-do-canal>` | baixa os pins novos (só entra arquivo; o indexer pega sozinho depois) |
| 2 | **Desktop** | `Desktop\` | o trabalho do dia — o resto deste arquivo |
| 3 | **Canal** | `<pasta-do-canal>\VIDEOS` + `CURTOS` | vídeo **já no ar** sobe pro Drive e sai do SSD |
| 4 | **Canal de lives** | `<pasta-do-canal>\<canal-de-lives>` | só o **corte longo do autor** fica; VOD e trechos → Lixeira |
| 5 | **Parceria** | `<pasta-da-parceria>\Video` | clips → Drive · vídeo inteiro → Lixeira · legenda → RAG |
| 6 | **Studio** | `<pasta-de-renders>` | sobra de render (quadro de tira, cache, faixa) de projeto parado → Lixeira; código e entrega ficam |
| 7 | **Índices** | banco do `<tarefa-indexer>` + disco | arquivo **truncado** e **fantasma** no índice |

O detalhe de cada uma está em [`references/rotinas-disco.md`](references/rotinas-disco.md) —
**abra antes de tocar em arquivo**, não trabalhe de memória.

⚠️ **Por que os índices ficam por último:** as frentes 2–6 movem e apagam arquivo, e cada
movimentação cria fantasma novo no índice. Podar antes é trabalho que você refaz.
E o Pinterest vem primeiro porque é a única que só **adiciona** — o que chegar já entra no
acervo antes de qualquer varredura.

> 🔴 **Quem aciona é o usuário (preferência do autor).** A skill não roda sozinha, não vira tarefa agendada, não é
> "oferecida" no fim de outra conversa. Se ele pedir só uma frente ("limpa só a parceria"),
> faça só ela — mas o default de `/limpar` é a passada completa.

### O gate que substitui o "ele escolhe a pasta"

Antes, a proteção contra mexer em projeto vivo era ele acionar pasta por pasta. Numa passada
completa isso não existe mais — então a proteção vira **regra dentro da passada**:

- arquivo **modificado nos últimos 3 dias** → HOLDING, nunca Lixeira (pode estar em uso agora);
- projeto de edição (`.prproj`, `.wfp`, `.veg`, **draft do CapCut**) que referencia a mídia →
  **não toca na mídia** — prova lendo o projeto (`scripts/capcut-refs.py`, recursivo; ver
  frente 3 do `rotinas-disco.md`). Se a mídia tem **gêmeo por hash no disco de mídia**, a saída melhor é
  **reapontar** o draft (`scripts/capcut-relink.py`, CapCut fechado, backup automático) e aí
  o Desktop esvazia;
- pasta que devolveu **0 DUP inteira** → é sinal de que nada dali nunca foi arquivado, não de
  que é tudo lixo novo (a armadilha da Fase 3 vale em todas as frentes).

**As 3 leis das rotinas** (valem também no Desktop):

1. **Texto e código nunca vão pra Lixeira** — vão pro RAG, sem perguntar. São leves, e o autor
   guarda coisa "inútil" de propósito (preferência do autor): no dia que for útil, não ter é que sai caro.
   ⚠️ O indexer **só lê `.md`** no vault — código arquivado precisa de uma nota `_INDICE.md`
   ao lado, senão você guardou e perdeu ao mesmo tempo.
2. **"Subiu pro Drive" precisa de prova.** `Test-Path` no Drive local só diz que o DriveFS aceitou
   localmente. Confirme na nuvem (MCP do Drive: `search_files` → `get_file_metadata`, batendo
   o tamanho) **antes** de mandar o original pra Lixeira.
3. **Só Lixeira, e avisa.** O usuário esvazia com os olhos dele — você lista o que mandou e por quê.

## A regra que manda

**Limpar ≠ organizar.** O entregável é o **Desktop vazio**. Arrumar bonitinho e deixar tudo lá
é fracasso. Mas o preço de esvaziar **nunca** é perder arquivo — todo arquivo sai por uma das
três portas, e nenhuma delas é "sumiu":

| porta | quando | pra onde |
|---|---|---|
| **ARQUIVA** | sei o que é, e é único | TEXTO → `<pasta-do-drive>` (RAG) · MÍDIA → `<pasta-de-midia>` |
| **LIXEIRA** | passou no GATE (já salvo, ou lixo) | Lixeira do Windows (recuperável) |
| **HOLDING** | não sei o que é / saída única / incerto | `<pasta-de-midia>\_temp\limpar-<data>\` + linha no relatório |

Na dúvida entre LIXEIRA e HOLDING → **HOLDING**. Sempre. Guardar custa zero; apagar é
permanente (já custou 3 arquivos do autor — [[feedback_seguranca_arquivos]], nota não incluída).

## Primitivas (carregue antes de tocar em arquivo)

```powershell
. "<pasta-de-skills>\limpar\scripts\limpar.ps1"
$s = New-LimparSessao          # cria <pasta-de-midia>\_temp\limpar-<data>\ + log
$desk = Get-DesktopLimpar
Get-LimparContagem $desk       # contagem ANTES (confere no fim)
```
`Move-Limpar` · `Move-ParaHolding` · `Remove-Limpar` (só Lixeira; **recusa se estourar o teto
da Lixeira** — cheia, ela expurga o mais antigo calado) · `Get-LixeiraFolga` · `Test-DupConfirmada` ·
`Find-NoDisco`. **Não improvise `Remove-Item`** — `-Force` pula a Lixeira e é irreversível.

---

## Fase 1 — identificar. NUNCA chutar.

Desça a escada e **pare no primeiro degrau que responder**:

**1. É óbvio?** (nome + pasta + contexto da sessão) → segue.

**2. Não é óbvio → ABRE.** O nome mente. UUID não diz nada.
- `.md`/`.txt` → **Read** · imagem → **Read** (você enxerga imagem)
- vídeo → `ffprobe` (duração/resolução/áudio) + frames via `ffmpeg`. Julgar o vídeo **inteiro**
  (moção, não 1 frame) → **agy/Gemini multimodal**
- áudio com fala → **Whisper cloud**:
  `<pasta-do-canal>\_maxine\maxine.cmd transcribe "ARQ" --lang pt-BR --model whisper`
  ⚠️ isso **cria um `.txt` do lado do vídeo, no próprio Desktop** — ele também vira item da limpeza
  (e o conteúdo dele é bom demais pra jogar fora: vai pro RAG).
- `.zip` → lista as entradas antes de decidir

> 🧩 **Muita saída gerada com nome UUID? Faça um contact sheet.** `scripts/mosaico.py` monta um
> grid rotulado — você lê **1 imagem** e identifica **N peças** de uma vez, em vez de queimar
> contexto abrindo 17. Foi assim que os 7 cards da leva e os 6 da diretoria foram mapeados.
> `python mosaico.py saida.jpg <img1> <img2> ...`

**3. Abri e ainda não sei de quem é → BUSCA SEMÂNTICA.** Tudo é embedado, inclusive as nossas
conversas. Se o caminho mais curto é perguntar ao índice, pergunte:

```powershell
python "<pasta-do-drive>/obsidian/_rag-tools/search.py" "<tema>" --top 5                    # vault
python "<pasta-do-drive>/obsidian/_rag-tools/search.py" "<tema>" --index pontual --top 5    # NOSSAS CONVERSAS + _temp
python "<pasta-do-canal>/_clip-search/search.py" "<tema>" --top 8 --index trabalho  # mídia de cliente
```
Índices do clip-search: `trabalho`, `flowout`, `clips`, `sfx`, `imagens`, `footage`, `geek`. Os nomes `*.json` antigos ainda funcionam.

**4. Nem o índice sabe → HOLDING + relatório.** Escreve "não identifiquei X, me diz o que é" e
**segue**. Não chuta, não apaga, não trava.

---

## Fase 2 — arquivar. SÓ mover.

**Nunca no mesmo passo que deleta** — foi misturar move+delete que perdeu os 3 arquivos.

> ⚠️ **Arquive a partir da lista CLASSIFICADA (o CSV da triagem), nunca de um glob novo.**
> Em 14/07 um `*.png` inocente varreu junto uma `logo-<cliente-farmácia>.png` que eu nunca tinha olhado —
> era duplicata, foi parar na pasta de fotos de produto. **Glob pega o que você não conferiu.**

Tabela de destinos, nomes de cliente por drive e o card com segundo destino (site do cliente):
[`references/destinos-arquivo.md`](references/destinos-arquivo.md) — **abra antes de mover**.

- TEXTO → `.md` com frontmatter no vault (o índice **só lê `.md`**; `.txt` copiado entra morto) ·
  MÍDIA → `<pasta-de-trabalho>\<cliente>\...` · vídeo/saída de IA → `3-videos\` · não sei → HOLDING.
- **Os nomes de cliente NÃO batem entre o Drive e o disco de mídia** (ex.: um grupo de 4 óticas com
  um nome por marca no Drive, outra árvore no disco, e um nome legado). Confira o destino real e a
  contagem depois de mover; não fechou → **para** e investiga.
- Card do **`<cliente-sindicato>`** tem um 2º destino (cópia pra grade do site) e, antes de copiar, confira
  nome e cargo de quem aparece na peça contra a `CARTA.md` do cliente.

---

## Fase 3 — o GATE de deleção (isolado, depois de tudo arquivado)

```powershell
& "<pasta-de-skills>\limpar\scripts\triagem.ps1" -Csv <pasta-de-midia>\_temp\limpar-<data>\triagem.csv
```
Indexa `<pasta-de-trabalho>` + `<pasta-do-canal>` por **nome E tamanho** (metadado, não lê conteúdo), e só então
**hasheia os candidatos**. Cospe `DUP` (gêmeo idêntico existe) vs `UNICO` (não existe → ARQUIVAR).

Só vai pra Lixeira o que passar em **TODAS**:
1. ✅ O gêmeo existe **no filesystem** do disco de mídia/Drive — *"está no índice" NÃO autoriza apagar*.
2. ✅ O conteúdo **bate por hash**. Nome igual, tamanho igual ou cosseno alto **não bastam**
   (cosseno ~1.0 ≠ mesmo arquivo — quase apagou B-roll bom).
3. ✅ **Não é saída gerada única, variação, nem nada incerto.**
4. ✅ **Re-confira o gêmeo NA HORA de apagar** — nunca confie no CSV; ele pode ter envelhecido.

Falhou em qualquer um → **HOLDING**. Nunca toca em `desktop.ini`/`.lnk`/`thumbs.db` (o script recusa).

### ⚠️ Texto não passa por hash contra o disco de mídia — ele vive no Drive
Detalhe, script de medição e as armadilhas de ponteiro:
[`references/gate-texto-e-ponteiros.md`](references/gate-texto-e-ponteiros.md) — **abra antes de apagar texto**.

`copy.txt`/`prompt.txt` **sempre** voltam como `UNICO` na triagem, mesmo já estando no RAG (a
triagem varre o disco de mídia, e o texto mora no Drive). Pra texto o teste é **conteúdo CONTIDO no canônico**.

**MEÇA a % de linhas contidas — não olhe e ache, e não confie no precedente.** Compare
**linha a linha** (só as ≥40 chars, espaço normalizado) e **exija 100%** (o script está no
reference). 🔴 Sobreposição parcial não é redundância — é conteúdo que só existe ali; par que já
foi redundante não segue redundante, meça sempre.

Não fechou 100% → **absorve o delta pro RAG** (uma nota `.md` consolidada por dia/kit, com
frontmatter), **reconfere 100% contra a nota nova**, e só então apaga.

### 🪤 Nota do RAG que APONTA pro Desktop não é backup
Achou o texto "no RAG"? **Resolve o wikilink e confere o conteúdo lá dentro.** Canônico que só
**aponta** pro Desktop (inclusive num campo do frontmatter: `entrega:`/`saida:`/`kit:`/`pasta:`)
→ o Desktop é a **única** cópia → **absorve pro RAG primeiro, conserta o ponteiro**, e só então
apaga. **Uma pasta inteira voltando 0 DUP é o sinal** de que nada dali nunca foi pro disco de mídia. E a
forma mais perigosa é a doc que AFIRMA que o backup existe ("os originais estão todos no disco de mídia"):
em 12/08 eram **58 artes de 10 clientes só no Desktop**. (O arquivamento das artes no disco de mídia é do
`/limpar`; a skill `peca` só entrega no Desktop.)

⇒ **Regra:** frase de doc **nunca** substitui o gate. Quem responde "já está no disco" é o hash, não o
texto — e menos ainda um texto escrito *antes* de o passo rodar.

🔎 Sinal do outro lado: pasta de DESTINO que existe e está **VAZIA** é o mesmo buraco do "0 DUP"
(comando de listagem no reference).

---

## Fase 4 — fechar

1. **Conserte os ponteiros** das notas que citavam o Desktop → agora citam o path no disco de mídia.
   ⚠️ `_kit-anexos-*` e `_pacote-posts-*` são **cópia verbatim** do que foi pro Desktop — o
   teste de contenção 100% depende disso. Não reescreva o corpo deles: o ponteiro novo vai
   numa chave do frontmatter (`desktop_esvaziado:` / `estado_no_limpar:`), com aspas simples.
2. **⛔ NÃO rode builder de índice.** `build_index*.py` é legado — rodar na mão fura a fila e
   gasta cota no mesmo item que o serviço já vai pegar. **Quem indexa é o `<tarefa-indexer>`**
   (serviço único em `<pasta-do-indexer>`, desde 27/07/2026; os 3 drips estão DESABILITADOS — não os
   religue). Confira que está vivo: `python <pasta-do-indexer>\status.py`.
3. **Relatório** em `<pasta-de-midia>\_temp\limpar-<data>\_RELATORIO.md` **e resumo inline no chat** (o usuário lê
   o chat, não navega o RAG): o que foi arquivado e **onde** · o que foi pra Lixeira e **por quê**
   · ⚠️ **o que não identifiquei** · ⚠️ o que parecia **em voo**.
4. `Get-LimparContagem $desk` → tem que dar **0** (fora `desktop.ini`).
5. Prove que é recuperável: `(New-Object -ComObject Shell.Application).Namespace(10).Items().Count`
   (⚠️ `ls '$RECYCLE.BIN'` no Git Bash **mente** — trata `$R*` como variável de shell).

## Quando PARAR e perguntar (1 pergunta, em lote)

Autonomia é o default — não pergunte onde salva, não peça permissão pra arquivar. Pare só se:
- a peça parece **em voo** (gerada hoje, ainda não postada/aprovada) e tirá-la do Desktop pode
  atrapalhar o fluxo dele;
- ia apagar algo que **não passou no gate** mas você "acha" que é lixo → **não apaga, pergunta**;
- o arquivo é **sensível/likeness** (foto de cliente/paciente — [[feedback_fotos_cliente_lgpd]], nota não incluída).

## Contexto que a skill assume

- `<pasta-de-entrega>` é a staging do protocolo `peca` — **descartável por desenho**
  ([[feedback_protocolo_coleta_anexos]], nota não incluída). Logos/refs ali são cópias reproduzíveis → Lixeira.
  Mas **saída gerada** ali dentro **não é reproduzível** → salva no disco de mídia antes.
- Postou → limpa. Não postou → a peça ainda **sai** do Desktop (vai pro destino definitivo), mas
  entra no relatório como pendente, **com o path**.
- Deletar no Drive (`<pasta-do-drive>`) cai na **Lixeira do Google Drive** (nuvem, 30 dias), não na do Windows.
