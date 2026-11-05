---
name: media-tools-local
description: >
  Ferramentas de midia e CLI instaladas localmente na maquina do usuario - ffmpeg,
  Whisper (openai-whisper + faster-whisper), yt-dlp, demucs, Python, Node, git. Use
  SEMPRE que a tarefa precisar transcrever audio/video, baixar video do YouTube,
  cortar / converter / inspecionar video, extrair audio, separar faixas (voz x
  instrumentos), ou rodar qualquer ferramenta de linha de comando de midia local.
  Da o inventario do que esta instalado e as receitas de invocacao prontas.
---

# media-tools-local - toolchain de midia local

Toolchain de midia/CLI da maquina do autor (Windows + RTX 3060; adapte o inventario ao seu). Inventario completo + versoes + paths na nota RAG `reference_ferramentas_cli` (nao incluida). Esta skill = receitas operacionais.

**Host:** Windows; `pwsh` 7.6 preferido, PowerShell 5.1 como fallback. Caminhos com espaco -> aspas. Tudo package-managed (WinGet/pip/npm).

## O que tem

Versão: rode `<ferramenta> --version`.

- **ffmpeg / ffprobe** - direto no PATH.
- **yt-dlp** - pacote pip, SEM .exe no PATH -> `python -m yt_dlp`.
- **Whisper** - `openai-whisper` (CLI: `python -m whisper`) e `faster-whisper` (lib, mais rapido).
- **demucs** - separacao de audio -> `python -m demucs`.
- **Python**, **Node**, **git**. Libs: torch/torchaudio, opencv, av (PyAV), pydub.

## Receitas

### Transcrever audio/video
> ⚠️ **DEFAULT = CLOUD (preferência do autor; o porquê medido está no fim deste parágrafo).** Fala → `maxine.cmd transcribe "ARQ" --lang pt-BR --model whisper` (`maxine.cmd` = wrapper do autor para o NVIDIA NVCF, não incluído; picota e perde parte da fala: serve pra entender, nunca como régua). **Timestamp por palavra → `gemini-3.5-transcribe`** (picota em 3-5 min e pode apagar nome próprio recorrente; lote só na chave paga). **Fallback, verificação e áudio que não pode subir → `faster-whisper large-v3` local em cuda.** Fala de cliente = `large-v3` em cuda/float16; CPU só com a GPU ocupada; nunca `small` (erro 22/06/26: "odor free"→"odofri", "EPA"→"hepa"). **Benchmark (180s, RTX 3060): cloud 6,8s vs local 39,9s** ⏳ medido em 10/07/2026 — o cloud é primário por **velocidade (~6x)**, não por hardware.

CLI rapido (openai-whisper):
```
python -m whisper "video.mp4" --model large-v3 --language pt --output_format srt --output_dir .
```
Models: `tiny` < `base` < `small` < `medium` < `large-v3` (qualidade x tempo). Fala de cliente = `large-v3`.

Mais rapido (faster-whisper, como lib):
```python
from faster_whisper import WhisperModel
m = WhisperModel("large-v3", device="cuda", compute_type="float16")
segs, info = m.transcribe("video.mp4", language="pt")
for s in segs:
    print(f"[{s.start:.1f}-{s.end:.1f}] {s.text}")
```

> Timestamp por palavra no local: `word_timestamps=True, vad_filter=True` (`m.transcribe(audio, language="pt", word_timestamps=True, vad_filter=True)` -> cada `s.words` tem `.start/.end/.word`); use quando o Gemini picotar ou o áudio não puder subir. Pipeline de decupagem completo na memoria `feedback_silencio_picotar_nao_colapsar` (nao incluida); timestamps por palavra em `notas/video/reference_transcricao_timestamps.md`.

### Baixar do YouTube
Video (melhor mp4):
```
python -m yt_dlp -f "bestvideo[ext=mp4]+bestaudio[ext=m4a]/best[ext=mp4]/best" --merge-output-format mp4 -o "saida.mp4" "URL"
```
So audio (mp3):
```
python -m yt_dlp -x --audio-format mp3 -o "audio.%(ext)s" "URL"
```

### ffmpeg - cortar / converter / inspecionar
Cortar sem re-encode (instantaneo, corta no keyframe):
```
ffmpeg -ss 00:01:30 -to 00:02:15 -i in.mp4 -c copy out.mp4
```
Corte exato (re-encode):
```
ffmpeg -ss 00:01:30 -to 00:02:15 -i in.mp4 -c:v libx264 -c:a aac out.mp4
```
Extrair audio:
```
ffmpeg -i in.mp4 -vn -acodec libmp3lame out.mp3
```
Inspecionar (duracao, codecs, resolucao):
```
ffprobe -v error -show_format -show_streams in.mp4
```

### Separar faixas de audio (demucs)
```
python -m demucs "musica.mp3"
```
Gera `separated/htdemucs/musica/` com vocals/drums/bass/other.wav.

### Pipeline pronto: YouTube -> transcricao -> clipes
`<pasta-de-ferramentas>\scripts\video_pipeline.py` (script do autor, nao incluido) baixa, transcreve com Whisper, corta clipes por segmento e concatena. Editar `VIDEO_URL` no topo e rodar:
```
python "<pasta-de-ferramentas>\scripts\video_pipeline.py"
```

### Cortar silencio (talking-head) - por NIVEL de audio
`<pasta-de-ferramentas>\scripts\silence_cut.py` (script do autor, nao incluido) detecta silencio com ffmpeg `silencedetect` (NAO por transcricao). Funciona com VIDEO (.mp4) E AUDIO puro (.wav/.m4a/.mp3 - detecta sozinho; saida de audio mantem a extensao de entrada). Exporta de dois jeitos:
- `--mode collapse` -> video com os silencios removidos (= VidClean, mas local, sem upload)
- `--mode segment`  -> PICOTA em pedacos fala/SILENCIO separados, nomeados c/ tempo+tipo (preferencia do autor: arrastar pro CapCut e deletar os SILENCIO na timeline = controle total, igual o Silence Detection do Filmora que ele perdeu)
```
python "<pasta-de-ferramentas>\scripts\silence_cut.py" "in.mp4" --noise=-30 --d 0.2 --pad 0.05 --mode segment
```
Knobs (equivalencia Filmora): `--noise` = limiar/"tolerancia" em dB (mais negativo = mais suave: -35 suave, **-30 equilibrado**, -25 agressivo). `--d` = duracao minima do silencio (Filmora usava 0.2s). `--pad` = respiro p/ nao cortar palavra (subir p/ 0.08-0.1 se ficar seco). Use `--noise=-30` (com `=`, p/ argparse nao confundir com flag).
Validado 15/06/26 em clipe real do autor: -30dB/0.2s empatou com o VidClean; segmentos arrastados no CapCut OK. `collapse` e frame-exato (filter_complex); `segment` usa fast-seek (pedaco de fala pode comecar 1 frame torto - se incomodar, peca modo frame-exato). Ver memoria `feedback_silencio_picotar_nao_colapsar`.

## Gotchas

- `yt-dlp` e `whisper` NAO tem .exe no PATH - sempre `python -m yt_dlp` / `python -m whisper`.
- `ffmpeg`/`ffprobe` estao no PATH (chamada direta).
- Corte `-c copy` so corta em keyframe; pra corte preciso, re-encode.
- Whisper na 1a run baixa o modelo (cache em `~/.cache/whisper`).
- Inventario / versoes / paths completos: nota `reference_ferramentas_cli` no RAG.

## Local (RTX 3060, CUDA ativo)

- `faster-whisper` com `device="cuda", compute_type="float16"`, modelo `large-v3`. CPU só com a GPU ocupada.
- Nunca `small` pra fala de cliente (erro 22/06: "odor free"→"odofri").
- ⚠️ A **rota padrão** (cloud vs local) é decisão do autor (preferência do autor), não consequência do hardware — ver topo desta skill.
