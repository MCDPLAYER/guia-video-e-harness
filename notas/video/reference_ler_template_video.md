---
name: reference_ler_template_video
description: "quando recall: como ler um vídeo de referência (ex. gravação de tela de um template do CapCut rodando) e descobrir quantos takes tem, quanto dura cada um, o movimento de câmera (zoom/pan/rotação) e o easing (ease-in/ease-out/linear)? como saber se a footage de um take é foto animada (Ken Burns) ou vídeo real sem confiar no olho? como detectar a área útil / o crop certo numa gravação de tela por variância de pixel? por que estourou RAM / travou a máquina processando vídeo em Python com numpy (np.stack)? como confiro se o crop bateu no formato certo (9:16 = 0,5625)? que filtro do ffmpeg detecta corte de cena além do select=gt(scene) (scdet)? como monto um contact sheet de frames com timestamp pra revisar o tracking? qual a receita dos 7 passos pra tracking de template de vídeo?"
metadata: 
  node_type: memory
  type: reference
  modified: 2026-08-25T18:05:49.510Z
---

Método pra pegar um vídeo de referência — tipicamente uma **gravação de tela de um template do CapCut rodando** — e extrair OBJETIVAMENTE (sem confiar no olho) quantos takes tem, a duração de cada um, o movimento de câmera (zoom/pan/rotação), o easing de cada movimento, e se a footage de cada take é foto animada (efeito Ken Burns) ou vídeo real. Stack: Python + OpenCV + ffmpeg + PIL, tudo LOCAL, custo zero. Validado 25/08/2026 lendo o template da `<cliente-farmácia>` (screen recording 1080x1920, crop final batendo 9:16 exato).

1. **Metadados brutos.** `ffprobe -v error -select_streams v:0 -show_entries stream=width,height,r_frame_rate,duration -of json VIDEO.mp4` → fps, resolução, duração total. Base pra converter frame↔timestamp nos passos seguintes.

2. **Detectar a área útil (só quando o vídeo é gravação de tela).** A UI do editor (barra, timeline, botões) fica ESTÁTICA; só o preview do template se move. Acumular variância temporal por pixel em STREAMING — soma e soma-de-quadrados, amostrando 1 frame a cada 8 (não precisa de todos):
```python
import cv2, numpy as np
cap = cv2.VideoCapture(video_path)
s = ss = None; n = 0; i = 0
while True:
    ok, frame = cap.read()
    if not ok: break
    if i % 8 == 0:
        g = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY).astype(np.float64)
        if s is None: s, ss = np.zeros_like(g), np.zeros_like(g)
        s += g; ss += g * g; n += 1
    i += 1
var = ss / n - (s / n) ** 2
```
Linhas/colunas com `var` ~0 nas bordas = UI parada → definem o crop. Validado: `crop=1035:1838:23:58` num 1080x1920 → área útil batendo 9:16 (conferir no passo de verificação abaixo).

3. **Cortes entre takes.** Filtro `scdet` do ffmpeg — mais legível que `select=gt(scene,X)` (o que a nota [[reference_ffmpeg_scene_cut]] usa pra picotar B-roll):
`ffmpeg -i VIDEO.mp4 -vf "scdet=threshold=10" -an -f null - 2>&1 | grep scdet`
Sai `lavfi.scd.score` + `lavfi.scd.time` no stderr por frame candidato a corte — score alto = corte real. Vira a lista de timestamps que delimita cada take.

4. **Movimento de câmera por take.** Entre frames CONSECUTIVOS do take (já recortados na área útil do passo 2): `goodFeaturesToTrack` (Shi-Tomasi) acha pontos → `calcOpticalFlowPyrLK` segue eles no frame seguinte → `estimateAffinePartial2D` vira isso numa matriz de rotação+escala+translação (sem shear):
```python
p0 = cv2.goodFeaturesToTrack(gray_prev, maxCorners=200, qualityLevel=0.01, minDistance=8)
p1, st, _ = cv2.calcOpticalFlowPyrLK(gray_prev, gray_next, p0, None)
M, _ = cv2.estimateAffinePartial2D(p0[st == 1], p1[st == 1])  # 2x3
```
ACUMULAR a matriz frame a frame (compor as transformações — não comparar só um par isolado) pra ter a curva de movimento do take inteiro. De `M = [[a, b, tx], [c, d, ty]]`: `scale = sqrt(a²+c²)` (zoom), `tx, ty` = pan em pixel, `rotação = atan2(c, a)` em graus.

5. **Easing.** Com a curva de escala acumulada do take, dividir os frames em 3 terços e comparar a velocidade média (Δscale por frame) em cada terço: **lento→rápido = ease-in**, **rápido→lento = ease-out**, **constante = linear**. (Raciocínio, ainda não validado num take real: terço do meio mais rápido que os dois das pontas sugeriria ease-in-out.)

6. **Foto animada (Ken Burns) vs vídeo real.** Pegar dois frames do take separados por *k* quadros, alinhar o frame `t+k` ao frame `t` usando a transformação acumulada (`cv2.warpAffine`) e olhar o MAPA de resíduo (`cv2.absdiff` + `cv2.applyColorMap` pra heatmap):
   - resíduo só em **contorno fino de borda** → é só erro de alinhamento (arredondamento do tracking) → a cena é FOTO estática, o movimento inteiro é o zoom/pan do editor.
   - resíduo espalhado em **região/textura** (tecido balançando, líquido, pele, cabelo) → tem movimento PRÓPRIO na cena além da câmera → é vídeo real.

7. **Contact sheet.** Montar com PIL, colando frame a frame numa grade, com rótulo de índice + timestamp por célula — é o artefato final pra revisar o tracking visualmente (e pra mostrar pro usuário sem ele ter que assistir o vídeo inteiro de novo):
```python
from PIL import Image, ImageDraw
sheet = Image.new("RGB", (cols * cw, rows * ch), "black")
draw = ImageDraw.Draw(sheet)
for idx, (thumb, t) in enumerate(frames):
    x, y = (idx % cols) * cw, (idx // cols) * ch
    sheet.paste(thumb.resize((cw, ch)), (x, y))
    draw.text((x + 4, y + 4), f"#{idx} {t:.2f}s", fill="yellow")
```

Quando usar: o usuário manda um vídeo de referência (concorrente, template CapCut, inspiração de Reels) e pergunta "como foi feito esse movimento" — em vez de chutar no olho, rodar essa receita e responder com número (quantos takes, quanto dura cada um, que tipo de easing). Ponte com geração: dá pra usar o resultado (escala/pan/rotação/duração extraídos aqui) como parâmetro de prompt pra replicar o movimento gerando um take equivalente no Omni/Veo.

## Pegadinha + como conferir

- ⚠️ **RAM, não GPU — não carregue o vídeo inteiro em memória.** `np.stack` de 518 frames 1080x1920 = ~3,2 GB de array; e o `np.stack` precisa ter a LISTA de frames E o array final vivos ao mesmo tempo durante a montagem → ~6,5 GB de PICO. Travou a máquina do autor (16 GB de RAM) e mandou o Windows pra swap — a RTX 3060 não tem nada a ver, isso é 100% CPU/RAM (esse tracking roda em CPU, não pede GPU). **Padrão certo:** processar em STREAMING (um frame — ou um par de frames consecutivos, pro optical flow — por vez, nunca a lista inteira) e reduzir pra CINZA e resolução pequena (518x920, ~metade do crop 1035x1838 do passo 2) antes de rodar o tracking. Cinza corta 3x (1 canal vs 3) e meia-resolução corta mais ~4x (metade em cada eixo) — tracking de movimento de câmera não precisa de resolução nem cor cheia, só a malha de cantos.
- **Como conferir se o crop do passo 2 bateu certo:** calcular `largura / altura` do crop achado e comparar com um formato CONHECIDO — não aceitar um número qualquer. `9:16 = 0,5625` · `4:5 = 0,8` · `1:1 = 1,0` · `16:9 = 1,778`. Exemplo validado: `crop=1035:1838` → `1035/1838 ≈ 0,563`, bate com 9:16 dentro da margem de detecção por pixel (sombra/borda da UI empurram o limiar da variância uns px — não precisa bater na casa decimal, só ficar a ~1% do formato conhecido). Se a razão não bater com NENHUM formato padrão, o limiar de variância errou (sobrou UI no crop, ou cortou parte do preview) — ajustar o limiar e rodar de novo antes de seguir pro passo 3.

Ver também [[feedback_video_julga_mocao_nao_frame]]: essa receita é pra EXTRAIR os números do movimento (a matemática de zoom/pan/rotação), não pra JULGAR se o resultado ficou bom — pra avaliar qualidade/moção de um vídeo GERADO (Omni/Veo), a regra continua sendo assistir, não ler frame.
