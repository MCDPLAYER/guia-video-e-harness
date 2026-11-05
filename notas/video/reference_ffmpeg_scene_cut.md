---
name: reference-ffmpeg-scene-cut
description: "quando recall: como cortar um vídeo em clips por CENA com ffmpeg? por que PySceneDetect/OpenCV trava/0-frames em av1? por que o nvenc não corta nos cut times (forced-idr)? como picar B-roll sem vazar sessão de GPU/exaustão? por que o nvenc PAROU de funcionar / 'Could not open encoder' / gera 0 bytes (ffmpeg 8.1.2 do winget exige driver 610, GTX 950 EOL só tem 582)? cadê o ffmpeg n7.1 local com nvenc? por que o geek_split morria silencioso sem log (bug cp1252 no subprocess text=True)? por que a extração de FRAME p/ thumbnail gera 0 frames / 'Could not open encoder before EOF' / 'Nothing written' em clip CURTO (-ss fixo antes do -i estoura o fim)? por que o ffmpeg sai exit 0 e NÃO escreve arquivo nenhum extraindo quadro de .jpg ('Output file is empty, nothing was encoded') — o -ss 0 no demuxer image2, e por que png/webp/gif não sofrem?"
metadata: 
  node_type: memory
  type: reference
  modified: 2026-08-13T12:27:26.551Z
---

Pipeline ROBUSTO pra picar um vídeo em scene-clips (B-roll), validado no `geek_split.py` (`<pasta-do-projeto>\_clip-search`, script do autor, não incluído) em jun/26 nos AMVs da `<parceria-musical>`. **100% ffmpeg** (abandonei PySceneDetect):

1. **Detectar cortes:** `ffmpeg -i SRC -vf "select='gt(scene,0.2)',metadata=print" -an -f null -` → extrai os `pts_time:` do **stderr** (= timestamps de mudança de cena). thr 0.2 ≈ punchy (~1 corte/2-3s); 0.3 = menos cortes/clips mais longos. Depois filtrar pra impor **mín 1.5s entre cortes** (descarta corte perto demais do anterior).
2. **Cortar (1 ffmpeg/FONTE, nunca 1/cena):** segment muxer —
   `ffmpeg -i SRC -an -c:v h264_nvenc -cq 20 -pix_fmt yuv420p -forced-idr 1 -force_key_frames <cuts> -f segment -segment_times <cuts> -reset_timestamps 1 OUT-S%03d.mp4`

`-an` = mudo (copyright de música). `-pix_fmt yuv420p` = av1 10bit→8bit. 4K do original preservado. Irmão do índice de anime ([[project_clip_search]], nota não incluída); índice/uso em [[project_indices_secundarios]] (nota não incluída).

## Pegadinha + como conferir
- **OpenCV/PySceneDetect TRAVA em av1** (`Progress 0%`, 0 frames pra sempre): a GTX 950 não tem HW av1 e o OpenCV não usa libdav1d. **Só o ffmpeg decodifica av1.** Por isso detecção via ffmpeg-select, não `scenedetect`.
- **`split-video` do PySceneDetect spawna 1 ffmpeg/CENA** → com centenas de clips **vaza sessão nvenc/handle → exaustão de recurso** (ffprobe/ffmpeg param de spawnar; falhas instantâneas em cascata, todas no mesmo segundo). Por isso 1 ffmpeg/fonte (segment muxer).
- **Sem `-forced-idr 1` o nvenc NÃO põe IDR nos `-force_key_frames`** → o muxer só corta nos keyframes esparsos dele (ex: **16 clips em vez de 37**). SEMPRE `-forced-idr 1` com nvenc + segment.
- **Proxy reencodado SUAVIZA os cortes** (a compressão apaga transições) → sub-detecta. Detectar no ORIGINAL full-res (ffmpeg aguenta av1 4K).
- **Conferir:** rode 1 av1 4K + 1 h264; cada clip sai na resolução do original, `audio=False`, e ~50-100 clips num AMV de 3-6min (NÃO 16). `ffprobe -v error -show_entries stream=codec_type,width,height -of json CLIP`.

## 🖼️ `-ss 0` num JPEG = exit 0 e ARQUIVO NENHUM (ffmpeg 8.1.2, medido 12/08/2026)

Extraindo quadro de **imagem** com `-ss` antes do `-i`, o ffmpeg sai **0 (sucesso)** e não
escreve nada: `Output file is empty, nothing was encoded (check -ss / -t / -frames parameters
if used)`. Medido em **10 de 10 `.jpg`** — inclusive `-ss 0`, que "não pula nada". Sem o `-ss`,
os mesmos 10 saem normais.

⚠️ **Só JPEG.** `.png`, `.webp` e `.gif` passam com `-ss 0` (testados). É o demuxer `image2`
com mjpeg: a imagem tem duração de 1 frame (0,04 s) e o seek cai fora dela.

**Why:** é o irmão do "`-ss` fixo estoura clip <1s" desta nota, mas **pior de detectar**, porque
o código de saída é ZERO — quem só checa `returncode` conclui sucesso e segue com um derivado
inexistente. No indexador isso já matou um item ("saída vazia" → deu duas vezes → morreu).

**How to apply:** em extração de quadro, **não mande `-ss` quando ele for 0** (não serve pra
nada) e trate imagem sem `-ss`. E em qualquer wrapper de ffmpeg, **exit 0 não é prova** —
confira `existe(saida) and tamanho > 0`.

## ✅ RESOLVIDO na 3060 (10/07): o nvenc global voltou
O ffmpeg 8.1 global faz `h264/hevc/av1_nvenc` na 3060 → o workaround da build n7.1 isolada virou **opcional** (dá pra reverter as consts `FFMPEG`/`FFPROBE` do `geek_split.py` pro global; não é urgente). **O bug cp1252 abaixo SEGUE VÁLIDO — não é hardware.**

## ⚠️ ffmpeg novo quebra nvenc (GTX 950) + bug cp1252 que matava o geek_split SILENCIOSO (2026-06-29)
- **winget auto-atualizou o ffmpeg p/ 8.1.2 (27/06) e o nvenc MORREU:** `Could not open encoder ... Required driver 610+`. A GTX 950 (Maxwell) trava no driver **582** (EOL, não há 610). Sintoma = encode nvenc gera **0 bytes**; decode av1 e libx264/CPU seguem OK (só o nvenc quebrou). **Fix:** build **ffmpeg n7.1** (BtbN gpl, tem nvenc) isolada em `<pasta-do-projeto>\_clip-search\ffmpeg-nvenc\bin`; o `geek_split.py` usa ela sozinho (consts `FFMPEG`/`FFPROBE` no topo, fallback pro global se sumir). NÃO downgradar o ffmpeg global (drip/porta-http/maxine usam). Teste: `ffmpeg -f lavfi -i testsrc=size=3840x2160:duration=1 -c:v h264_nvenc x.mp4` → >0 bytes = OK. CapCut renderizando NÃO atrapalha (2 sessões nvenc cabem no driver 582).
- **Bug cp1252 (corrigido):** `subprocess.run(..., text=True)` decodificava o stderr do ffmpeg como cp1252 (locale Win) → `UnicodeDecodeError` na thread leitora → `det.stderr=None` → `TypeError` não-tratado → o geek_split **morria sem logar nada** (parecia teardown/OOM/CapCut — me enganou por horas). Só dispara em AMV com byte não-cp1252 no stderr. **Fix:** `encoding="utf-8", errors="replace"` em TODO subprocess com capture (probe_wh + detecção). **Lição: rodar em FOREGROUND com 2>&1 revela o crash que o `pythonw` sem console engole — não chutar teardown/OOM sem ver o stderr real.**

## ⚠️ Extração de FRAME p/ thumbnail: `-ss` fixo estoura clip curto (2026-07-02)
Escopo ≠ scene-cut — é a extração de **1 frame representativo** p/ thumbnail/descrição VLM (`build_nim_media.py` no `_rag-tools`, pipeline NIM mídia→texto; irmão do `clip_lib.py`).
- **Bug:** `ffmpeg -ss 1 -i CLIP -frames:v 1 -vf scale=512:-2 f.jpg` — o `-ss 1` (seek ANTES do `-i`) num clip **< 1s** cai além do fim → decoder dá EOF sem frame → o mjpeg encoder nunca abre → **"Could not open encoder before EOF" / "Nothing was written" → 0 frames**. Comum em AMV/SFX curtos (poluía o `_drip_nim.log`; clip fica SEM descrição de cena, cai calado do índice).
- **NÃO era o pix_fmt:** o warning *"Non full-range YUV is non-standard"* que aparece junto é RUÍDO — medido: acervo é ~99% `yuv420p` e 50/50 + 160/160 dos clips **≥1s** passavam no comando antigo. Só os **<1s** falhavam (provado: 1.001s e 0.917s davam 0 frame). A hipótese pix_fmt-10bit foi REFUTADA pela amostra antes de eu gravar o fix — lição de não canonizar o suspeito óbvio sem medir.
- **Fix (root cause):** fallback de seek — `for ss in ("1","0")`: tenta `-ss 1` (evita fade-in), cai p/ `-ss 0` (frame 0) se não gerou; + `-pix_fmt yuvj420p` (silencia o warning, formato que o mjpeg aceita) + `-an` (ignora áudio).
- **Conferir:** `ffprobe -show_entries format=duration` p/ achar clips <1.2s → comando ANTIGO (só `-ss 1`) não gera `.jpg`; NOVO (com fallback ss=0) gera. Vale do próximo run do drip NIM em diante. Irmão do [[project_clip_search]] (thumbnail do `clip_lib.py` já tinha `-pix_fmt yuvj420p` desde 04/06, mas NÃO o fallback de seek).
