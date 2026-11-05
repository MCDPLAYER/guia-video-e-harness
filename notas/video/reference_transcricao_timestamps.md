---
name: reference-transcricao-timestamps
description: "quando recall: como transcrever com TIMESTAMP por palavra pra CORTAR vídeo/short (achar in/out dos cortes)? o vídeo JÁ TEM .srt do editor — ainda preciso transcrever? por que o maxine transcribe --model whisper só devolve TEXTO sem tempo? qual modelo NVCF dá word timing? como ligo enable_word_time_offsets no parakeet/riva? o corte fecha arrastando muleta ('mas enfim', 'e aí', 'bem') porque a palavra tá no MESMO cue — como acho o gap entre palavras? silencedetect não acha silêncio nenhum sob música? transcrição cloud pra decupagem/cortes? cadê o transcribe_ts.py? SUMIU um trecho da transcrição / faltou fala / o whisper comeu o retake (condition_on_previous_text)? como CONFERIR se a transcrição perdeu fala? posso usar a transcrição cloud pra validar corte (não)? Gemini 3.5 Transcribe presta / é melhor que o whisper / qual motor eu uso pra word timestamp em 2026? tempo de palavra de letra CANTADA / música (alinhamento forçado MMS_FA)? quanto custa e quantas req/dia dá o free do gemini-3.5-transcribe? silencedetect/volumedetect devolveu ZERO linha (é o -v error do ffmpeg, não o limiar)? o ASR comeu fala — como PROVO que comeu?"
metadata: 
  node_type: memory
  type: reference
  modified: 2026-11-03T18:57:45.180Z
---

## Passo 0 (ANTES de escolher modelo): o vídeo já veio com `.srt`?
🆕 **Se o editor/o usuário entregou um `.srt` junto do corte (legenda queimada tem fonte), NÃO transcreve nada.** O SRT é a régua — 550 cues, timing exato, zero ASR, zero erro novo. Verificado 10/07 nos cortes do `<video-ensaio-02>` (um ensaio do canal do autor): o `.srt` do vertical bateu 1:1 com o vídeo (último cue 15:33,4 × duração 15:35,3) e substituiu o parakeet + whisper inteiros que o `<video-ensaio-03>` tinha gastado.
- **Gate de alinhamento (1 comando, sempre):** último cue do SRT ≈ duração do `ffprobe`. Bate → usa. Não bate (SRT é de outro corte / outra versão) → aí sim transcreve.
- **O SRT é cue-level, não word-level.** Serve pra achar in/out de FRASE (que é o que corte de short precisa). Se precisar de palavra (silêncio, erro de fala) → seções abaixo.

---

Pra **CORTAR** vídeo sem SRT pronto (achar in/out de cortes) preciso de transcrição **com timestamp por palavra**. No cloud (maxine/NVCF) os dois modelos ASR NÃO são iguais nisso:

- **`--model whisper`** (whisper-large-v3 no NVCF) = **só TEXTO, ZERO timestamp** — mesmo com `enable_word_time_offsets=True` no `RecognitionConfig`, `resp.results[].alternatives[0].words` volta **vazio**. Ótima pontuação/acurácia; serve pra ENTENDER, não pra cortar.
- **`--model parakeet`** (parakeet-1.1b-rnnt-multilingual) = **popula word timing** (`words[]` com `.word`/`.start_time`/`.end_time` em ms). Texto um pouco mais cru (errinhos de ASR: "waifas"→waifus, "Song de Wu"→Sung Jin-Woo), mas o **timing é sólido**.
- 🆕 **`faster-whisper large-v3` LOCAL em `device="cuda"` (3060, desde 09/07) = texto do whisper COM word timing.** Verificado 10/07: 384 palavras com `.start`/`.end` em 180s de áudio. Pega o melhor dos dois — pontuação do whisper + timing por palavra — **sem depender do parakeet**. Custo: ~40s por 3min de áudio (14,1s são o load do modelo), contra 6,8s do cloud. Então: **cortar → local; só entender o texto → cloud.**

## 🆕 `gemini-3.5-transcribe` (medido 27/08/2026) — motor novo, ganha em TEXTO
Mesmo áudio nos 3 motores (locução PT-BR do canal do autor, ensaio `<video-ensaio-03>`):

| motor | 180s | 23,7min | word-ts | fala perdida |
|---|---|---|---|---|
| whisper NVCF (`maxine --model whisper`) | **8,2s** | 52,5s (picota em 5) | ❌ | **−17,1%** |
| **`gemini-3.5-transcribe`** | 14,5s | 66,8s (1 request) | ✅ 3548 palavras | 1 buraco de ~3s em 24min |
| faster-whisper large-v3 local (3060) | 28,3s (10s só de load) | — | ✅ | — |

- **Vocabulário do GÊNERO: só o Gemini acerta** — "isekai" (os DOIS whispers: "sekai"), "shounen", "manhua", "waifus", e *"que **não se** reinventa **há** mais de 15 anos"* (whispers: *"que nos reinventa mais de 15 anos"*). É LLM com contexto de linguagem, não ASR puro: ganha em estrangeirismo e concordância.
- 🔴 **MAS em arquivo LONGO ele APAGA nome próprio recorrente — e isso é o furo que decide a rota.** Mesmo áudio, mesmo dia: em recorte de **3min** ele escreve "Gojo" 4× (igual ao NVCF) e "Escanor" 3×; no arquivo de **24min** escreve **"Gojo" 0× (NVCF: 15×)**, "Escanor" 0×, "Satoru" 0× — sem variante de grafia, a palavra some e a frase fica agramatical (*"você ama o assiste o conhece o cara"*). Curiosamente "Saitama" sobreviveu 5× nos dois. **Não é erro de ASR, é omissão** — e num canal de anime a transcrição sem o nome do personagem não serve pra decupar por assunto.
  - ➡️ **Consequência: picotar NÃO é só pra respeitar o teto de 30min, é requisito de QUALIDADE.** Blocos de ~3-5min com sobreposição ~10s, costurados pelos timestamps. A vantagem "1 request sem emenda" é falsa em áudio longo.
  - ⚠️ **`custom_vocabulary` (que resolveria) é INCOMPATÍVEL com timestamp** → `400 custom_vocabulary is incompatible with timestamps`. Ou você tem os nomes certos, ou tem word timing — não os dois na mesma request.
- **Timestamp É confiável (testado, não suposto):** recortei 6 spans de ~3s **cegamente** pelos offsets do Gemini e transcrevi cada recorte no faster-whisper: **86% de casamento médio** (100/93/89/84/83/67%). Serve pra cravar in/out.
- **Chamada** (SDK `google-genai` ≥2.6, `client.interactions`, áudio SEMPRE pela Files API):
```python
GC={"transcription_config":{"mode":{"type":"verbatim","timestamp_granularities":["word"]}}}
f=c.files.upload(file=AUD)
it=c.interactions.create(model="gemini-3.5-transcribe",
    input=[{"type":"audio","uri":f.uri,"mime_type":f.mime_type}], generation_config=GC)
```
  Word timing sai em `steps[0].content[0].annotations[]` (`type="word_info"`, `start_offset`/`end_offset` tipo `"1.400s"`, + `start_index`/`end_index` casando no texto).
- ⚠️ **`mode:"smart"` CORTA CONTEÚDO, não só disfluência** — comeu *"onde eu desmontava uma série de pontos dos arquétipos"*, que é fala real. Decupagem = **sempre `verbatim`**.
- ⚠️ **Ligar timestamp derruba o teto de 1h → 30min por request.** Ensaio de 24min passa inteiro (1 request, sem emenda); live de 3h = picotar em 6 com sobreposição ~10s e costurar pelos timestamps — a emenda é justo onde o NVCF perde fala.
- 💰 **Free = 25 req/DIA nesse modelo** (não os "500 RPD" do e-mail; a 2ª chave free do autor dá **3/dia**) → lote NÃO cabe no free. Paga ~US$0,005/min (24min ≈ R$0,72). Cotas: [[reference_gemini_keys]] (nota não incluída).

## 🆕 Como PROVAR que o motor comeu fala (método fechado, 27/08)
1. `gap = word[i].start − word[i−1].end` → lista os `gap > 4s`.
2. Cruza com `silencedetect` do MESMO arquivo (**sem `-v error`**, ver pegadinha). Gap com >60% de silêncio = pausa real, descarta.
3. O que sobrar, **abre**: `ffmpeg -ss` no span + roda em **2 motores independentes**.
4. **Veredicto:** dois motores que discordam nas PALAVRAS mas concordam na ESTRUTURA = tem fala ali, o motor testado comeu. **Um motor sozinho afirmando fala NÃO é prova** — nesse mesmo teste o faster-whisper inventou um nome próprio que não existe no áudio. Ver [[feedback_inferencia_sem_prova]] (nota não incluída).
   Resultado real: 32 gaps>4s em 24min → 30 pausa, 1 falso positivo, **1 buraco verdadeiro**.

## 🆕⚠️ Áudio com RETAKE → `condition_on_previous_text=False` (senão o whisper COME a repetição)
O `faster-whisper`/`openai-whisper` default é `condition_on_previous_text=True`: o modelo lê o próprio output anterior como contexto e trata **texto repetido como loop de alucinação** → descarta o trecho. Numa locução de uma sentada (a do canal do autor SEMPRE é), o autor regrava a mesma frase 2-3× — que é **exatamente** o que eu preciso enxergar pra decupar. Resultado real (25/07, ensaio `<video-ensaio-04>`): a 1ª rodada **apagou 35s inteiros** (a entrada do Sukuna) sem erro nenhum, transcrição saiu "limpa" e mentindo. Re-rodei com `condition_on_previous_text=False` e o trecho voltou; o resto dos timestamps NÃO desalinhou (o whisper mantém o alinhamento, só omite o texto).
- **Gatilho:** áudio bruto de gravação (retake, false start, frase repetida). Em vídeo já editado/finalizado o default serve.
- **Custo:** zero de velocidade; só perde um pouco de coerência de pontuação entre segmentos.
- **Como conferir (1 script):** pra cada par de segmentos consecutivos, `gap = seg[i+1].start − seg[i].end`; cruza com o `silencedetect` do MESMO arquivo. Gap > 4s **que não é silêncio** = fala engolida. No teste apontou 2 buracos, os dois no mesmo lugar — e nenhum depois do fix.

⚠️ **Cloud (maxine/NVCF) PERDE fala nas emendas dos chunks** — ele picota >5,5min e some com palavras na costura de cada pedaço. Em 29min sumiu ~30% do texto. Serve pra **entender** (foi como detectei o buraco do local: os dois erram em lugares diferentes), **NÃO** serve como régua pra validar corte. Validação de decupagem = **local, com o mesmo modelo/flags** dos dois lados.

**Estratégia dupla (quando precisa dos dois):** rodo **parakeet** (régua de corte = timing) + **whisper** (texto limpo = entender a nuance) e **cruzo com o roteiro original no RAG** pra travar as palavras que o ASR errou. Os "buracos" do parakeet num vídeo com música alta são **drop de palavra, NÃO silêncio** (o whisper pega a narração ali) → dá pra cortar spans longos sem furo.

**Script durável (do autor, não incluído):** `<pasta-do-projeto>\_maxine\transcribe_ts.py` (reusa `maxine.get_key/FN/to_wav`; liga `enable_word_time_offsets`; picota >330s e soma offset pela duração REAL de cada chunk; cospe `.txt` + `.srt` + `.words.json` + `.segments.json`). Uso: `venv\Scripts\python.exe transcribe_ts.py IN OUTPREFIX --model parakeet` (`--probe 90` = só os 1ºs 90s pra testar).

**Why:** o `maxine transcribe` padrão foi feito pra gerar TEXTO (README diz saída `.txt`), então nunca extraiu tempo; a rota do CLAUDE.md ("transcrever fala → whisper") está certa **pra texto**, mas pra CORTAR o default é **parakeet**. Descoberto ao vivo cortando o ensaio `<video-ensaio-03>` (whisper deu 0 palavras num probe → troquei pra parakeet → 232 palavras com tempo).

**How to apply:** texto/legenda/entender → whisper. Cortar/decupar/achar in-out → **parakeet** (ou os dois + cruza roteiro). Contexto LOCAL diferente (whisper word-level local pra silêncio/erro de fala) em [[feedback_silencio_picotar_nao_colapsar]] (nota não incluída); inventário CLI em [[reference_ferramentas_cli]] (nota não incluída; ver a skill `media-tools-local`); motor NVCF em [[reference_nvidia_nim]] (nota não incluída).

## 🆕 Refinar a borda DENTRO de um cue (senão o corte fecha com muleta)
Um cue do SRT agrupa várias palavras. Fechar o corte no fim do cue **arrasta a muleta** e mata o banger: `"…contratada pela empresa errada mas enfim"` é UM cue (14:59,766→15:01,066). Cortar em 15:01 entrega o "mas enfim"; a frase quer morrer em "errada".

**Receita:** `ffmpeg -ss <t-2> -t 5 -i SRC -af "silencedetect=n=-20dB:d=0.05" -f null -` → os `silence_start` são os **gaps entre palavras**. Escolhe o gap certo e usa como OUT. (No `<video-ensaio-02>`: gap real em **900,60s**, contra 901,066 do cue.)

⚠️ **Limiar aqui é OUTRO** — não confundir com o corte de silêncio:
- **−30dB / d≥0,2** = tirar silêncio de talking-head ([[feedback_silencio_picotar_nao_colapsar]]).
- **−20dB / d=0,05** = achar gap ENTRE PALAVRAS pra cravar borda. Sob **música de fundo** o áudio nunca desce de −32dB → `silencedetect` a −30dB devolve **zero linha** e parece que "não tem pausa". Não tem bug: **sobe o limiar** (−20dB) e os gaps aparecem.
- Estimar a posição da palavra por regra de três dentro do cue (dividir a duração pelo nº de palavras) **é chute** e corta sílaba. Mede.
- **O tempo por palavra do faster-whisper também mente na borda** (`<cliente-ótica-2>`, 03/11). Ele deu "WhatsApp" em 3,02 s e "Zap!" em 3,36 s, mas pelo RMS o "WhatsApp" vai até 3,56 e o "Zap" começa em 3,88. Mutar em 3,27 comeu metade do "WhatsApp". Também pôs a 1ª palavra do take em 0,0, quando a voz entrava em 1,05. **Conferência:** recorto o áudio até o ponto de corte e transcrevo só o recorte. Se a última palavra sair inteira, o corte está certo.

## Pegadinha + como conferir
- 🆕🔴 **`silencedetect`/`volumedetect` com `-v error` = ZERO linha — e parece que "o áudio não tem pausa nenhuma".** Os dois filtros reportam em nível **INFO**; `-v error` engole a saída inteira e o `grep` volta vazio. **Não é o limiar.** Custou uma conclusão errada hoje (27/08): varri de -20dB a -45dB, todos deram 0 silêncio, e marquei 32 gaps como "fala engolida"; com o log ligado eram **254 silêncios** e só **2** suspeitos (1 deles falso positivo). **Ordem certa:** roda SEM `-v error` → se vier 0 linha aí sim mexe no limiar (`volumedetect` dá o `mean_volume` pra escolher: nesse arquivo -20dB de média, -30dB pegou 254).
- **SRT pronto > qualquer ASR.** Antes de transcrever, procura `.srt`/`.vtt` irmão do vídeo. É o erro caro: re-transcrevi no `<video-ensaio-03>` o que no `<video-ensaio-02>` veio de graça.
- **whisper NVCF ignora word offsets (0 words) — não é bug do teu código, é o modelo.** Se precisa de tempo e veio 0 palavra, TROCA pra parakeet, não fica debugando.
- **Conferir o corte exportado (não confia no plano):** `ffprobe` cada clip → `duração_real ≈ duração_planejada` (drift ≤ 1 frame) e `|dur_video − dur_áudio| < 0,05s`. Nos 48 cortes do `<video-ensaio-02>` pegou drift máx 0,02s e sync máx 21ms.
- **Conferir:** `python transcribe_ts.py ARQ probe --model parakeet --probe 90` → espera **>0 palavras com start/end** e um `.srt` com acento correto (UTF-8; se vier mojibake é só o terminal, o arquivo tá certo).
- Re-encode dos cortes: **nvenc da 3060 funciona** — `h264/hevc/av1_nvenc` no ffmpeg 8.1 global (verificado 10/07). Usar direto. *(O "nvenc não abre → libx264 veryfast / n7.1 isolada" era da GTX 950 no driver 582 EOL. Morto pós-09/07 — ver [[reference_ffmpeg_scene_cut]].)*
- **Letra CANTADA (lyric/re-edit de música) com a letra conhecida → alinhamento forçado, não ASR:** `<pasta-do-projeto>\<pasta-do-lyric>\align.py` (script do autor, não incluído; torchaudio MMS_FA na voz isolada pelo demucs) dá tempo por palavra e por letra; foi a régua de um lyric video 16:9 da `<parceria-musical>` aprovado em 19/10. Método em `<pasta-do-projeto>\lyric-01\LEIA.md` (não incluído).
