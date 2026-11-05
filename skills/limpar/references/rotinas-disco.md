# Frentes de disco — o `/limpar` fora do Desktop

O `/limpar` nasceu pro Desktop. Estas frentes aplicam a **mesma disciplina** (identificar →
arquivar → gate → relatório) nas pastas que engordam sozinhas: `<pasta-do-canal>`, `<canal-de-lives>`,
`<pasta-da-parceria>`, `<pasta-de-renders>`, os índices e as pastas do Pinterest.

> Rotina do autor (preferência do autor): as frentes são as pastas DELE e os scripts citados não estão
> incluídos. Reaproveite as leis, os gates e as cicatrizes medidas.

> 🔴 **Isto não são seis comandos — é uma passada só.** O autor acumula os jobs e chama
> quando tem tempo; quando ele chama, você percorre as sete frentes na ordem do `SKILL.md`
> e entrega **um** relatório. Não pergunte a cada etapa se pode seguir, e não rode nada
> sem ele ter acionado.

## As 3 leis que valem em todas

**1. Texto e código NUNCA vão pra Lixeira.** São leves e o autor guarda coisa "inútil" de
propósito (preferência do autor) — no dia que for útil, não ter é que é caro. Vão pro RAG, sempre, sem perguntar.

**2. "Subiu pro Drive" é uma AFIRMAÇÃO que precisa de prova.** `Test-Path` no Drive local só diz
que o DriveFS aceitou o arquivo localmente — **não** que ele existe na nuvem. Confirme pela
API do Drive (ver abaixo) antes de mandar o original pra Lixeira.

**3. Lixeira, nunca `-Force`.** O usuário esvazia depois, com os olhos dele. Você avisa o que
mandou, ele confirma. `Remove-Limpar` do `scripts/limpar.ps1` já faz certo — e **recusa
quando o item não cabe** até o teto da Lixeira do drive (`Get-LixeiraFolga`). Lixeira cheia
não dá erro: o Windows **expurga o item mais antigo calado**, ou seja, apaga de vez algo que
o usuário ainda podia recuperar. Recusou → para a frente, pede pra ele esvaziar, retoma.

### Como confirmar que subiu de verdade

```powershell
# 1. copia — SEM -Force. Destino existe com tamanho diferente = OUTRO arquivo com o mesmo nome
#    (em 28/09: 8 cortes da parceria com o nome de curtos já arquivados). Grava com sufixo.
if ((Test-Path -LiteralPath $destinoG) -and (Get-Item -LiteralPath $destinoG).Length -ne $len) {
    $destinoG = <mesmo nome + ' (curto-canal)' antes da extensão>
}
Copy-Item -LiteralPath $origem -Destination $destinoG
# 2. o DriveFS é assíncrono: o arquivo aparece no Drive local antes de existir na nuvem
```

Depois da cópia, confirme **na nuvem**, não no Drive local:

- **Preferido** — o MCP do Google Drive: `search_files` e `get_file_metadata`. Prova =
  **título exato** (comparado por fora: o `title =` da API é aproximado — "Parte 4" devolve
  a 1 e a 2) **+ `fileSize` igual + `createdTime` de hoje**. O terceiro campo não é enfeite:
  o mesmo título costuma existir em **outra pasta** do Drive, de um upload antigo (outro
  render, outro tamanho — ou, um dia, o mesmo tamanho), e ele "provaria" a sua cópia.
- **Fallback sem MCP** — `(Get-Item $destinoG).Length` igual ao original **E** o arquivo
  já não estar "só local". Se o DriveFS ainda estiver subindo, **espere**; não apague.

⚠️ **Não hasheie a cópia no Drive local pra "conferir".** Ler arquivo no Drive **baixa** ele
(estoura o cache — [[reference_drive_structure]], nota não incluída). Hasheie na **origem**, antes de copiar,
e compare com o **tamanho** que a API devolve. Para vídeo grande, tamanho + a API é
suficiente; o hash serve pro que é pequeno.

### Onde texto e código param

| o quê | destino | fica buscável? |
|---|---|---|
| nota, roteiro, copy, pacote, descrição | `.md` no vault (com frontmatter) | ✅ sim |
| transcrição `.srt`/`.txt` | converte pra `.md` no vault | ✅ sim |
| código (`.py`, `.ps1`, `.json`, `.bat`) | `<pasta-do-drive>\obsidian\_arquivo\<rotina>-<data>\` | ❌ **não** |

🔴 **O indexer só lê `.md` no vault** (e `.md`+`.txt` no `<pasta-de-midia>\_temp`). Confirmado no
`texto.py`: `rglob("*.md")`. Um `.py` ou `.srt` copiado pro vault fica **guardado e mudo** —
está lá, a busca não acha.

⇒ Por isso todo lote de código arquivado leva **uma nota `_INDICE.md` ao lado**, dizendo o
que tem na pasta e de onde veio. A nota é o que o recall acha; a pasta é o que guarda.
Sem a nota, você guardou e perdeu ao mesmo tempo.

🧷 **Path do Windows no frontmatter vai em aspas SIMPLES** (`midia_em: '<pasta-da-parceria>\...'`).
Em aspas duplas o `\t`, `\S`, `\<` viram escape ou erro, e o YAML inteiro da nota deixa de
ser lido (18 notas quebradas assim entre 11/09 e 28/09). Gerou nota por script → valida com
`yaml.safe_load` antes de gravar.

### O que é lixo temporário (esse sim, Lixeira)

`__pycache__`, `*.pyc`, `*.log`, `*.tmp`, `*.lock`, `.err`, caches de render, `_prep/`,
`_work/`, `audio16k.wav`, faixas intermediárias de demucs (`vocals.wav`, `*_alinhado.wav`).

---

## Frente 3 — Canal: vídeo publicado sai do SSD

**Alvo:** `<pasta-do-canal>\VIDEOS` e `<pasta-do-canal>\CURTOS`.
**Destino:** `<pasta-do-drive>\<arquivo-do-canal>\VIDEOS\<pasta do vídeo>\`.

### O gate: está mesmo no ar?

```powershell
python "<pasta-de-skills>\limpar\scripts\yt-publicados.py" "<pasta-do-canal>\CURTOS"
```

Varre os uploads do canal inteiro (paginado, inclui privado e agendado) e
cospe um CSV com veredito por arquivo. **Só `PUBLICADO` autoriza arquivar.**

| veredito | o que fazer |
|---|---|
| `PUBLICADO` | está no ar, processado, público/não-listado **e a duração bate** (`duracao-gate.py`) → sobe pro Drive → Lixeira |
| `DURACAO-DIVERGE` | o título casou com o upload **errado** (arquivo 73s × vídeo de 800s) → re-casa por duração |
| `PARTE-DIVERGE` | a série está no ar, a "Parte N" do arquivo não → re-casa por duração |
| `PRIVADO` | agendado ou rascunho → **não toca** (o vídeo ainda vai ao ar) |
| `NAO-PROCESSADO` | YouTube ainda processando → não toca |
| `NAO-ACHEI` | não casou → HOLDING + relatório |

🔴 **A cicatriz do gate (08/09/2026, primeiro teste real).** A versão ingênua do casamento
deu `PUBLICADO` pra **368 de 427** arquivos. Com o número da parte entrando na conta, caiu
pra **271** — as outras 97 eram `Parte 1..4` casando com a `(Parte 7)` que estava no ar, e
38 eram **privados/agendados**. Quase 100 arquivos de material não publicado teriam sido
apagados por "já postei essa música". ⇒ **Título parecido não é o mesmo vídeo.** Se um dia
o script for reescrito, o controle é este: `<Música X> Parte 3` **não** pode casar com
`<Música X> (Parte 2)`.

🔢 **Mas o número da parte também não é prova — a DURAÇÃO é.** A "Parte N" do YouTube é a
ordem de **postagem**; a do arquivo é a ordem do **corte**. Em 28/09, 8 arquivos
`PARTE-DIVERGE` estavam no ar com outro número (`<Música Y> - Parte 1` = `(Parte 2)` no YT,
0,0s de diferença). O DIVERGE não se resolve olhando — roda a re-casagem:

```powershell
python "<pasta-de-skills>\limpar\scripts\diverge-duracao.py" --sessao <pasta-de-midia>\_temp\limpar-<data>
```

Título-base parecido (tokens do arquivo **+ da pasta**: `respondendo comentarios\parte 1
completo.mp4` só casa pela pasta) + duração ±1,5s + casamento **1-pra-1** (um upload não
prova dois arquivos). Em 28/09: 83 DIVERGE → 9 provados no ar, 2 privados, 1 ambíguo e **71
que ainda não foram postados** (69 partes de CURTOS) — essas são a fila, não lixo.

⚠️ **A prova vale só na rodada feita ANTES de apagar.** O arquivo que foi pra Lixeira solta o
upload que ele provava, e o 1-pra-1 dá esse upload pro vizinho de duração parecida. Na
re-rodada de 28/09, a `(Parte 6)` de uma série (que era da Parte 4, já apagada, 0,5s de
diferença) passou pra Parte 2 (0,8s), que **nunca foi ao ar**. Hoje o script detecta que
tem arquivo sumido e marca todo PUBLICADO da rodada como `-SUSPEITO`.

### Antes de arquivar a pasta do vídeo

1. **Texto e código primeiro** (lei 1): `PACOTE-YT.md`, `PUBLICACAO-YT.md`, roteiro,
   `_ORDEM-UPLOAD-YT-*.txt`, `.srt` → vault.
2. **Projeto de edição vivo não sai.** `.prproj`, `.wfp`, `.veg`, `_work/`, **draft do
   CapCut** — se o projeto ainda abre esse vídeo, arquivar quebra o link. Na dúvida: **HOLDING**.
   "Abre esse vídeo" se prova **lendo o projeto**, nunca pelo nome:
   - CapCut: `scripts/capcut-refs.py --raiz <pasta>` — varre o draft **recursivo**. O path
     mora em `Timelines\`, `subdraft\`, `mini_draft.json`, `matting\` e em 4 grafias (`/`,
     `\`, `\\`, `\\\\`, com `\uXXXX`). Ler só o `draft_content.json` do topo deu "livre" pra
     5 drafts que ainda estavam presos.
   - `.wfp` do Filmora é **ZIP** (lê o JSON de dentro) e o `.prproj` é **gzip**. "Preso" por
     nome parecido foi falso positivo (`<projeto-A>` × `<projeto-B>\PROJ.wfp`, 28/09).
3. **Respeite a convenção que já existe.** O autor já usa `_ja-no-youtube\` dentro da pasta do
   curto. Isso é a marca **dele** de que postou — trate como pista, **não** como prova: o
   gate continua sendo o CSV.

---

## Frente 4 — Canal de lives: cortes de live

**Alvo:** `<pasta-do-canal>\<canal-de-lives>` (⏳ tamanho muda sozinho — meça na hora).
**Destino:** `<pasta-do-drive>\<arquivo-do-canal>\<canal-de-lives>\`.

O autor grava live (num canal secundário), eu acho os trechos, ele edita o corte final. **Só o corte final dele é
que fica.** O resto é recuperável (o VOD fica sempre disponível no canal dele) ou
regenerável (baixando o VOD de novo, a pipeline de cortes roda outra vez). ⇒ A perda aqui é
de **tempo**, nunca de conteúdo — por isso esta frente pode ser agressiva.

### 🔑 O critério é DURAÇÃO, e é do autor (preferência do autor, definido 08/09/2026)

> **"MEU corte é o longo mesmo, maior que um trecho de 180s e menor que o VOD."**
> **"Eu não faço cortezinho de 50s/180s — se existe, é saída sua."**

| duração | o que é | destino |
|---|---|---|
| **≤ 185s** | corte curto = **saída MINHA**, sempre | 🗑️ Lixeira |
| **> 185s, abaixo do VOD** | **corte do autor** | ⬆️ **Drive**, depois Lixeira local |
| a live inteira (pasta `live <data>\`) | VOD | 🗑️ Lixeira |

Use os três sinais juntos — **local + padrão do nome + duração** —, nunca a duração sozinha:

| o que é | como reconhecer | destino |
|---|---|---|
| VOD completo | está em `live <data>\` (sem `_saida`), horas de duração | 🗑️ Lixeira |
| trechos que EU achei | `*_saida\MELHORES-MOMENTOS\NNN_XhYYmZZ.mp4` | 🗑️ Lixeira |
| cortes curtos meus | `NN_slug_MMmSS_DURs.mp4` e derivados legendados, ≤185s | 🗑️ Lixeira |
| **corte longo do autor** | `FULL.mp4`, `full.mp4`, `MELHORES MOMENTOS LONGO.mp4`, `<lives> juntos.mp4`, outros nomes livres — **>185s** | ⬆️ **Drive** |
| faixas de áudio | `_faixas\*.wav`, `_prep\*.wav`, `audio16k.wav` | 🗑️ Lixeira |
| pacote, descrição, transcrição | `_PACOTE-YT.txt`, `_DESCRICOES-SHORTS.txt`, `_LEIA.txt`, `text\` | 📝 **RAG** |
| código do pipeline | `_pipeline\*.py` | 📝 RAG (`_arquivo\`, com `_INDICE.md`) |

🔴 **A cicatriz: eu classifiquei ao contrário na primeira versão desta página.** Marquei
`CORTES-APROVADOS\` como "o corte editado dele" (por causa do nome da pasta) e `FULL.mp4`
como lixo. O `ffprobe` desmentiu os dois:

- **toda** a `CORTES-APROVADOS\` tem 85–185s — inclusive os `corte-N.mp4` de
  `legenados\corte\`, que são os mesmos 5 cortes legendados (durações batendo: 185/183,
  165/165, 85/85, 182/182). É saída minha inteira. **Nome de pasta não é prova.**
- os `FULL.mp4` têm 214s, 800s e 1375s — e **não são concatenação automática**: a soma dos
  trechos da mesma pasta dá 2338s contra 800s do FULL. É seleção, ou seja, trabalho dele.

⇒ **Meça com `ffprobe` antes de classificar.** E quando o arquivo cair perto da fronteira
(o `full.mp4` de 214s do dia 23-08 está a 29s do limite), ele vai pro **Drive**, não pra
Lixeira: errar pra cima custa espaço, errar pra baixo custa o corte dele.

⚠️ `veadotube-mini.exe` na raiz é **ferramenta em uso** (avatar da live). Não toca.


---

## Frente 5 — `<parceria-musical>` (parceria)

**Alvo:** `<pasta-da-parceria>\Video\<música>\`.
**Destino:** `<pasta-do-drive>\<arquivo-do-canal>\<parceria-musical>\<música>\`.

> A parceria **não é canal do autor** — é parceria pra postar no TikTok deles
> ([[feedback_parceria_musical]], nota não incluída). Não confunda com o canal principal.

Cada pasta de música tem três coisas:

| o que é | como reconhecer | destino |
|---|---|---|
| vídeo inteiro baixado do YT | `.mp4` solto na raiz da pasta da música (`YTDown.com_*`, `* (1080p, h264, youtube).mp4`…, ~90 MB) | 🗑️ Lixeira (recuperável no YT pelo título) |
| clips/shorts cortados | subpasta `<slug>\` (`M1.mp4`, `S1.mp4`, `* - Parte N.mp4`) | ⬆️ **Drive** `<parceria-musical>\<música>\<slug>\`, depois Lixeira local |
| legendas / transcrição | `*.srt` (pt e en) | 📝 **RAG** (converte pra `.md`) |

**Ordem obrigatória:**

1. `.srt` → vault primeiro. Legenda é transcrição: é o que sobra da música quando o vídeo
   sumir.
2. **`.srt` de 0 byte é truncado** — o download falhou. Lixeira (é o caso dos
   `*.pt.srt`/`*.en.srt` vazios que convivem com os `[Portuguese]...srt` bons).
3. Clips: confira se **já estão** em `<arquivo-do-canal>\<parceria-musical>\<música>\`. Não estão → sobe.
4. 🔴 **Pasta sem clips cortados não é pasta limpa — é trabalho pendente.** Se só existe o
   vídeo inteiro e nenhum `M*/S*`, **não apague nada**: reporte "faltam os cortes de
   `<música>`" no relatório. Apagar o inteiro aí destrói a matéria-prima do corte que ainda
   não foi feito.
5. Só depois do clip confirmado na nuvem: vídeo inteiro → Lixeira. **Mesmo com draft do
   CapCut apontando pra ele** — critério do autor (28/09): *"se já viraram shorts e clips, pode
   dar como feitos"*. Feito = clips confirmados na nuvem **+ ≥1 short da música publicado**
   (`yt-curtos-v2.csv`). O draft fica com mídia offline, e isso é aceito. Música sem short
   publicado não entra nessa regra: o inteiro fica.
6. Atualize a nota de legenda (`_canal\<parceria-musical>\*.md`): `midia_em` passa a dizer Lixeira +
   data, e o path antigo vai pra `pasta_original_d`. 🔴 **Case nota↔pasta pela chave do
   frontmatter (`midia_em` = a pasta), nunca pelo slug buscado no texto** — em 28/09 o slug
   de uma música aparecia dentro da letra da nota de outra, e duas notas iam ser reescritas
   no lugar das certas.

⚠️ `_legendas\` é **ferramenta**, não saída: tem os `.py` do pipeline de tradução. Código vai
pro `_arquivo` só se o pipeline estiver morto — se está em uso, não mexe.

---

## Frente 6 — Studio: sobras de render do motion-studio

**Alvo:** `<pasta-de-renders>\<projeto>\`. **Destino:** só Lixeira. O que sai é
regenerável a partir do código do projeto, e o código nunca sai.

Cada vídeo feito por código deixa no disco de 5 a 10 vezes o próprio tamanho. Em 09/10/2026
eram 43 GB, e **20,6 GB eram quadro PNG de tira** (`range\**\r_NNNN.png`, 30 por transição
revisada).

```powershell
& "<pasta-de-skills>\limpar\scripts\studio-sobras.ps1" -Csv <pasta-de-midia>\_temp\limpar-<data>\studio.csv           # simulação
& "<pasta-de-skills>\limpar\scripts\studio-sobras.ps1" -Csv <pasta-de-midia>\_temp\limpar-<data>\studio.csv -Aplicar  # Lixeira
```

| camada | quando | o que sai | o que fica |
|---|---|---|---|
| **1** | projeto parado há **≥3 dias** | quadros da tira (`range\**\r_NNNN.png`) e stills avulsos (`stills\t_*.png`) | a tira montada `strip_*.png` e a folha `sheet_*.png` |
| **2** | parado há **≥7 dias** **e** com mp4 de entrega | `segs*\` (cache do render), `film_*.mp4` (vídeo sem áudio), `audio\stem_*.wav` e `mix_pre*.wav` | mp4 entregue, `final\`, `entregas\`, `voz*.wav`, `mix.wav` |

"Parado" = arquivo **mais novo** do projeto, em qualquer subpasta. "Entrega" = mp4 na raiz que
não começa com `film_`, ou qualquer mp4 em `final\`/`entregas\`.

- **Os 7 dias da camada 2 têm motivo:** apagar o `segs` obriga um re-render inteiro (~48 min
  no modo leve) se o cliente pedir ajuste. Quadro de tira se refaz em segundos, por isso a camada 1
  usa só os 3 dias de sempre.
- 🔴 **Nunca sai:** código e texto (`index.html`, `*.py`, `*.json`, `STORYBOARD.md`, `.srt`),
  `voz*.wav` (TTS pago que não sai igual de novo), `takes\`, `img\`, `assets\`, `refs\` (geração
  de IA ou insumo do cliente).
- Projeto **sem `render*.py`** está fora do padrão e o script não entra. Projeto parado **sem mp4
  de entrega** só perde a camada 1 e vai pro relatório como "confirmar com o usuário".
- O `-Aplicar` recalcula "parado" e "entrega" **na hora**: projeto reaberto depois da simulação
  é pulado.

---

## Frente 7 — Índices: truncado e fantasma

Duas sujeiras diferentes, com riscos diferentes.

### Truncado (arquivo 0 byte no disco)

```powershell
Get-ChildItem '<pasta-do-canal>','<pasta-de-trabalho>','<pasta-da-parceria>' -Recurse -File -EA SilentlyContinue |
  Where-Object { $_.Length -eq 0 }
```

🔴 **0 byte NÃO é sinônimo de truncado.** A varredura de 08/09 achou 121 arquivos de 0 byte
e a **maioria era legítima**: `__init__.py` de pacote Python (0 byte é o normal), `.gitkeep`,
`NOTE.txt` de plugin. Apagar "sem dó" quebraria o `nim-clients` do Maxine.

⇒ **Só é truncado o 0 byte de MÍDIA ou LEGENDA**: `.mp4 .mov .mkv .wav .mp3 .png .jpg .srt`.
Esses são download/render que falhou, e aí sim vão pra Lixeira sem cerimônia.
**Código e marcador de 0 byte: não toca.**

### Fantasma (está no índice, sumiu do disco)

O serviço é o **único escritor** do banco — pare antes, religue depois:

```powershell
python <pasta-do-indexer>\status.py               # vê "candidatos a sair do indice"
schtasks /end /tn <tarefa-indexer>
Get-Process pythonw | Where-Object { $_.CommandLine -like '*indexer.py*' } | Stop-Process -Force
python <pasta-do-indexer>\poda.py                 # SIMULAÇÃO (mídia)
python <pasta-do-indexer>\poda.py --texto         # SIMULAÇÃO (texto)
python <pasta-do-indexer>\poda.py --apply         # só depois de ler a simulação
schtasks /run /tn <tarefa-indexer>
```

- A `poda.py` tem **prova dupla**: ausente em 2+ varreduras saudáveis **E** ausente agora.
  Em 08/09 ela preservou 8 de 21 candidatos porque tinham voltado pro disco. Confie nela e
  **não escreva DELETE na mão** no banco.
- ⚠️ Confirme que o serviço morreu de verdade: `schtasks /end` encerra a tarefa mas o
  `indexer.py` pode continuar vivo. O teste honesto é o mutex:
  `python -c "import sys; sys.path.insert(0,r'<pasta-do-indexer>'); import poda; print(poda.indexer_rodando())"`
- Ao religar, o próprio serviço devolve pra fila os jobs que ficaram `running` sem dono.
- **Nunca rode `build_index*.py` na mão** — quem indexa é o `<tarefa-indexer>`, e os dois juntos
  gastam cota no mesmo item.

### Órfão de renomeação (o caso que a poda não pega)

Arquivo renomeado deixa um asset velho com **0 vetores** e jobs que tentam pra sempre
(`FileNotFoundError`, 14 e 18 tentativas em 08/09). A `poda.py --texto` **não** acha, porque
ela procura documento com vetor. Sintoma no `status.py`: item "feito sem vetor cujo ARQUIVO
sumiu". Confirme que o nome NOVO já está indexado (tem vetores) antes de tirar o velho.

---

## Frente 1 — Pinterest: pins novos entram no acervo

O autor pina a semana toda; o acervo dos editores em `<pasta-do-canal>` tem que acompanhar.

```powershell
cd <pasta-de-trabalho>\_ferramentas\midia-dl
python pin.py --lista boards.txt --sync -o <pasta-do-canal>
```

Os boards de `boards.txt` caem em 4 pastas de acervo (memes, croma, pngs, moodboard).
Depois disso **não faça mais nada**: as 4 estão dentro de `<pasta-do-canal>`,
que é raiz do indexer — ele varre e indexa sozinho no ciclo seguinte.

- `--sync` baixa **só o que entrou depois da última vez**. Rode sempre o mesmo comando.
- 🔴 **Não mova nem apague o `.historico.sqlite3`** da pasta de destino. Ele é a memória do
  que já baixou; sem ele o sync rebaixa tudo. E material sincronizado que muda de lugar
  precisa levar o `.historico` junto ([[reference_download_massa_pinterest_tenor]], nota não incluída).
- Arquivo que o usuário apagou de propósito **não volta** — é o desenho. Pra forçar, `--refazer`.
