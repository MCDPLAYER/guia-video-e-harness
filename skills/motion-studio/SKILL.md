---
name: motion-studio
description: Motion design por CÓDIGO no nível dos virais do Opus, com um REPERTÓRIO DE ESTILOS separados (UI, vidro líquido, Kitey, 32Bits, papel). Cobre UI motion, demo de produto, explainer, vídeo-ensaio do canal, colagem, animação procedural e vídeo com locução. Use quando o usuário pedir "faz um vídeo/animação/motion de X", "demo do app", "vídeo de produto", "reels animado", "explainer", "edita no estilo do 32Bits/Kitey", "motion igual aqueles do Opus", ou quando for mexer em qualquer vídeo gerado por código (HTML, Canvas, Remotion, HyperFrames). NÃO serve para vídeo de IA generativa (esse vai pela gflow) nem para editar gravação real (media-tools-local).
---

# motion-studio

Método medido na POC-01 (28/09/2026). Às cegas contra um viral de UI motion de um criador de referência (11,6 mil likes): perdeu na v2, ganhou na v3.
- Pesquisa: nota `PESQUISA-MOTION-DESIGN-OPUS-VIRAIS-2026-09.md` do cofre de notas do autor (nota não incluída).
- Motor e ferramentas em `ref/` (os .py/.html não vieram neste repo, só os .md): `poc01-index.html`, `render-vsl.py`, `finish.py`, `lyria.py`/`bed.py`, `caps.py`, `sfx.py`, `REVIEW-PROMPT.md`.
- **A estética mora em `estilos/`, um arquivo por estilo.** Este arquivo é só o que vale pra todos.

> **Teto: ~150 linhas aqui e ~60 por estilo.** Lição nova = UMA linha, reescrevendo a que existe: no estilo se é daquele estilo, aqui se vale pra todos.
> **Ideia de fora não entra: vai pro `ref/EM-TESTE.md`** (⏳ versão B, o usuário promove).

> 🔒 **GATE 0 — a cada BLOCO novo e depois de CADA compactação, antes de qualquer código: ABRIR (Read) as imagens da seção "Abrir antes" do arquivo do estilo.** Prova = uma linha no `BLOCOS.md` do projeto: `bloco · imagens abertas · o que levo de cada`. Sem a linha, não tem código. O `BLOCOS.md` é o que o hook `estado-pos-compact` reinjeta depois de compactar (junto com o estilo que ele cita): projeto sem ele não é lembrado. O texto do estilo e o código do bloco anterior NÃO substituem olhar: o min 3 de um ensaio do canal (21/10) saiu sem personalidade por isso.

## Papéis (dentro de motion, valem por cima das regras gerais de delegação)
- **O Opus escreve TODO o código da peça e olha o resultado.** O código é a decisão estética; delegar a escrita é delegar o gosto.
  - POC-02 (19/10), mesmo brief às cegas: Opus 7,0 · Argon 6,0 · DeepSeek 5,5.
  - Modelo novo se mede na bancada (nota `BANCADA-MOTION-MODELOS.md`, não incluída).
  - Subagente só como capítulo paralelo, com guia do Opus, sem teto de turnos.
- **Olho = Claude lendo folhas e tiras.** Gemini não entra.
- **Revisor = Claude Opus NOVO, às cegas, contra um viral de referência DO MESMO ESTILO** (gate 4). Ele não edita.
- **Régua final = o usuário.** Render pronto e conferido → abro na tela dele já carregado: `wscript "<pasta-do-projeto>\_ferramentas\video-feedback\video-feedback.vbs" "<render.mp4>"` (H.264; ferramenta local do autor, não incluída). Ele anota ali; `<video>_feedback\FEEDBACK.md` ao lado do render vira a fonte (tempo + quadro + texto + PNG desenhado): leio as pendentes e abro cada PNG antes de mexer.
- **Cliente:** em vídeo, os guards de estilo e as proibições visuais do brand system NÃO valem (o autor, 28/09). Do cliente ficam o **tema** e a **verdade** (preço, recurso e fluxo conferidos no site no ar; nada de depoimento, número de resultado ou selo inventado). Paleta e fontes da marca são material, não prisão. **Fonte se escolhe por projeto, casando com o mood, nunca a de sempre por reflexo** (o autor, 27/10: "tu tem um vicio de usar uma fonte só"). Na entrega, diga qual fonte usou e por quê. Fonte por destino:
  - **Cliente com assinatura de fontes (`<cliente-E>`):** fonte do Envato Fonts, na conta do cliente. Não tem API: o usuário baixa e eu instalo da pasta.
  - **Canal e outros clientes:** só Google Fonts ou fonte de licença livre. Nada da Envato no canal (risco de copy).

## 1. Escolher o ESTILO (antes de tudo)
**Uma peça, um estilo.** O autor pediu estilos separados em 19/10: misturar tudo num estilo-mestre faz um engolir o outro.
- **Como escolher:**
  - Leio o arquivo do estilo escolhido e só ele.
  - Se o pedido não deixa óbvio qual estilo usar, **pergunto ao usuário com 2 opções e o porquê**. Ex.: ensaio do canal pode ser Kitey ou 32Bits; produto de cliente é UI.
- **Misturar estilos:** só quando o usuário escolher. Ex.: 32Bits com uma abertura em vidro. Mesmo assim, cada trecho segue a gramática do seu estilo; não se faz média.
- **Estilo novo:** nasce de uma referência medida, que vira peça teste e entra no `EM-TESTE`. Quando o usuário aprovar, vira arquivo aqui e linha na tabela.

| Estilo | Quando | Arquivo | Peça de referência (status) |
|---|---|---|---|
| **UI** | produto, app, fluxo, dado; padrão de cliente | `estilos/ui.md` | POC-01, reels e VSL do `<cliente-app>` (✅) |
| **Vidro líquido** | abertura premium, iOS/One UI, título com material | `estilos/vidro.md` | `ref/glass-01/` (✅ 16/10) · lib `shaders` (⏳) |
| **Kitey** | vídeo-ensaio do canal, narração reflexiva, câmera num palco | `estilos/kitey.md` | `<video-ensaio-01>` v3.2 + um Short 9:16 (✅ 19/10) |
| **32Bits** | ensaio ou explicador de energia alta, colagem pop, cliente jovem | `estilos/32bits.md` (+ `32bits-cardapio.md`) | COMP-01 (✅ 19/10) |
| **Papel** | explicador calmo, colagem Vox em stop-motion | `estilos/papel.md` | explicador do canal `?papel` v4 (⏳ "ok pra teste") |

Também testado, sem arquivo ainda: **pixel nativo** (peça da `<parceria-musical>`, registro em `ref/EM-TESTE.md`, aguarda a régua).

**Piso comum a TODOS os estilos** (o resto é do estilo): quadro = função pura do tempo · todo estado tem causa visível · nenhum quadro vazio sem intenção · nenhuma cor intermediária suja · nenhum órfão · o que a borda cortaria no repouso sai antes, texto nunca fatiado.

**Material e referência (o autor, 19/10):**
- **Mídia de fora é o padrão** nos estilos que colam material (32Bits, Kitey, Papel, híbrido): Pinterest (busca logada no Chrome; board inteiro = `midia-dl`), Tenor (`tenor.py`), web. O acervo/croma é UM lugar, não o limite: fora clipe de anime, o vault ainda é pequeno. Busca pelo gesto ou princípio, não pelo tema (o tema devolve o clichê). Tudo que entra ganha `_PROCEDENCIA.md` (arquivo → origem → uso); rosto de pessoa real = pergunto antes.
- **Gramática de edição pra qualquer estilo** (transição rara com motivo, zoom com razão, função por elemento, som que confirma o olho): cardápio em `ref/gramatica-de-edicao.md` (✅ min 6 de um ensaio do canal, 23/10), com o gap em aberto voz→tom e o diagnóstico "ritmo certo, quadro pobre".
- **Referência já extraída não se extrai de novo:** abra as folhas e tiras salvas (`<pasta-de-renders>\ref-criadores\<id>\fundo\` e, cortada em cenas com `_folhas\` e `_INDICE.jpg`, `<pasta-de-midia>\Inspiração de edição\EDIÇÃO\<nome>\`). Ref nova analisada → as folhas ficam salvas lá.

## 2. Escolher a rota técnica (o estilo diz o QUE; a rota diz COM O QUÊ)
| Rota | Stack |
|---|---|
| 1. HTML/DOM | `index.html` + `seek(t)` (`ref/poc01-index.html`) |
| 2. Canvas procedural | Canvas/p5 + pincel, papel, boil; mesmo `seek(t)` (motor do `<video-ensaio-01>` / comp-01) |
| 3. Híbrido | imagem Nano (0 créd) / clipe Omni (gflow) / chromakey do acervo + código por cima, em volta ou redesenhando. Eixo de cada um (menu, não regra): `ref/ASSETS-GERADOS.md` |
| 4. 3D / shader | Three.js/GLSL, lib `shaders`, WebGL2 próprio (ver `estilos/vidro.md`) |

Remotion e HyperFrames são opcionais; a ferramenta não é o diferencial. Na dúvida, `index.html` puro sem npm.

## 3. Brief (formato de um criador de referência de UI motion, ~40 linhas)
`inputs` (cliente, fatos PROVADOS, assets) · `direction` (**estilo** + referência NOMEADA + 5–8 regras tiradas do arquivo do estilo + **Banned**) · `structure` (grid de beats) · `build` · `gotchas` · `start`.

Quem escreve o STORYBOARD é o próprio modelo, **antes** do código. É uma tabela: tempo · causa → efeito · *reads* (o que o olho lê primeiro).

## 4. Motor (copiar de `ref/poc01-index.html` ou do projeto aprovado do estilo)
- **`window.seek(t)`:** o quadro é função pura de `t`, sem estado.
- **Render:** `render-vsl.py` (Playwright + Chrome local, `channel='chrome'`).
  - **4 subquadros por quadro.** 8 só no segmento que cruza `window.HISUB` (giro ou voo rápido, onde 4 deixam 3–4 cópias); nunca 8 no filme inteiro, que dobra a captura. `sub=n` força.
  - Os quadros vão em JPEG q95 direto pro pipe do ffmpeg, e `tmix`+`select` dá motion blur real a 60 fps.
  - **Segmentos de 2 s**, retomáveis por `.done`.
    - Fix: apague o `.done` **da chave ANTERIOR à seguinte**, porque a mola passa da janela editada.
    - **Fix com render rodando não pega:** pare, corrija e retome.
- **Um NAVEGADOR por worker, nunca abas:** abas dividem o processo de GPU (⏳ 30/09: 4 abas 55 min → 4 navegadores 27 min). **Em 4K, no máximo 2 navegadores nos 16 GB** (cada um ~2,4 GB com python + decode 10 bits + NVENC + Chrome; o 3º derruba a RAM pra ~1 GB em 20 s). Use um processo novo por trecho e não guarde o PNG da captura em variável, porque o Playwright vaza ~33 MB por quadro. Look/cor/prumo do bruto vai na placa (torch+NVENC, 2,7× a CPU, mesmo quadro). O gargalo 4K é a captura do Chrome, não a placa. Bench: nota `BENCH-CPU-GPU-2026-10-29.md` (não incluída; ⏳ reconferir se trocar RAM/placa/Chrome).
- **Encode padrão = `enc=nvenc` qp 18** (VMAF 98,2 × 97,9 do x264 crf 14, ~45% menos CPU).
  - O encoder não se mistura no mesmo filme: trocar = apagar todos os `.done`.
- **Padrão `modo=leve`** (2 navegadores, prioridade ociosa; ⏳ 09/2026, reconferir a partir de 12/2026): o usuário trabalha no PC durante o render (preferência do autor). `modo=turbo` só com o PC livre. **Nunca 2 renders juntos** (~1 GB por worker, 16 GB). A ociosa cede a vez a qualquer job normal (~20× mais lento): prévia pesada só entre renders. Matar por PID, nunca por cmdline.
- **Helpers:** `step(tau, period, z)` = mola em forma fechada · `track(t, [[t0, v0]…])` soma uma mola por troca de alvo e herda a velocidade · `vis(t, tin, tout, dur, od)` entrada/saída · `show`/`place` em coordenadas do mundo.
- **Câmera:** pan em x na MESMA mola da forma, só menos elástica.
- **`window.EVENTS`:** a mesma timeline gera o SFX e as marcas de beat.

## 5. Gates, nesta ordem, sem pular
1. **Stills:** 1 quadro por beat → folha de contato, **aberta ao lado da folha da referência** (estilo com "Abrir antes"). Procurar órfão, borda, hierarquia e o que a ref tem e a nossa não. Pista de cor: `ref/cor-folha.py`.
   - 📁 Quadro, tira, take e stem vão numa SUBPASTA da peça.
   - Filme, folha final e capa ficam soltos na raiz da peça ou em `final\` (sem `_` na frente). O RAG do autor só lê isso (`escopo.py` do indexador, não incluído).
2. **Tiras:** 30 quadros consecutivos a 60 fps de CADA transição (`render-vsl.py strips nome=t0`). Ler célula por célula. **Os defeitos que dão cara de "porco" só existem aqui.**
3. **Filme:** folha + tiras extraídas do **mp4 final**. Toda correção passa de novo pela tira, porque o fix cria defeito novo.
4. **Revisor às cegas:** subagente `general-purpose`, `model: "opus"`, NOVO a cada rodada, prompt = `ref/REVIEW-PROMPT.md`.
   - A = a nossa; B = viral do mesmo estilo. Folha + 2 tiras de cada.
   - **Decidir pelos P1, conferidos na célula; não pela média** (a nota oscila ~1 ponto sem a peça mudar).
   - P1 descartado ganha o motivo escrito no projeto.
   - Falso positivo existe: um acento que a miniatura engoliu, ou um "dissolve" que é um quadro de motion blur num corte seco. Conferir no 1080.

## 6. Áudio, locução e legenda
- **SFX = som de BANCO real; o código só posiciona** (A/B `<cliente-E>` 26/10, o autor escolheu "banco real": o earshot deu 0,85 real × 0,14 pro `sfx.py`). A síntese só fica se o `realness` aprovar o trecho. Banco: canal = Taira Komori (`<pasta-de-midia>\efeitos sonoros\taira-komori\`, aparar ~0,1 s de silêncio nas pontas) / Freesound CC0. Cliente com licença própria (`<cliente-E>`) = + Epidemic/Envato. Na busca de sfx, whoosh e pop vêm errados: escolher por categoria e ouvir. **Com voz + trilha, som de chiado largo vira "ruído" (o autor, 27/10, `<cliente-G>`)**: meça a fração acima de 6 kHz antes de usar (pop_into_head −3 dB, magic_waves/vanishing ≈ −17 = chiado; reflection/drop ≈ −22/−57 = tonal), e a trilha substitui a cama/ambiente. Método e fontes: nota `PESQUISA-SFX-2026-10-26.md` (nota não incluída).
  - **Paleta por vídeo:** um clique, um pop, uma família de whoosh, um material. Ação repetida puxa de 4+ takes, sem repetir seguido, ±1–2 dB. Classe que tem som tem som SEMPRE.
  - **Tempo sai do `EVENTS`, alinhado pelo PICO:** pop no meio do percurso · clique até 1 quadro antes · pico do whoosh no pico da velocidade · riser acaba 30–250 ms antes do hit. Tolerância −45/+75 ms.
  - **Causa na tela:** whoosh só em troca de mundo ou câmera grande; corte seco não ganha som. Com fala: efeito fora de 1–4 kHz e hit no respiro, nunca na palavra-chave, com a trilha abaixando no hit.
  - **Densidade e volume = régua do usuário, não a norma** (preferência do autor). No A/B ele achou o banco "tímido": pops baixos, subtexto e legenda pedindo som, "pouco pra quantidade de elementos". O som também é dramático: dog respirando, "trimilique", relax → fogos.
  - **Ambiência:** room tone contínuo + uma cama por lugar, inclusive em cena de animação. Entra antes da imagem (J-cut), no nível "muta e faz falta".
  - **Emoção:** silêncio de 0,2–0,7 s (com um fio de som) antes da virada e um impacto real por reveal, afinado ou sem nota.
  - **Portão:** `earshot` (`<pasta-de-ferramentas>\earshot\.venv\Scripts\python.exe -m earshot`, CLI, ~50 s por chamada, só CPU; não é MCP global por causa da RAM). `sheet` (sincronia + espectro) vai junto da folha de vídeo, antes do revisor. `profile` das refs do cliente dá a régua.
- **Locução + trilha são o PADRÃO de toda peça** (preferência do autor, 27/10, reels do `<cliente-G>` só com SFX: "sem musica e sem narração fica mal feito"); sem voz ou sem música só quando ele pedir, e o gasto (Lyria/TTS pago) se pede no INÍCIO, junto com o roteiro, nunca depois do render.
- **Locução:** Gemini TTS → timestamps por palavra com `faster-whisper` local. O início de cada palavra fica ancorado no fim do silêncio anterior. **Os beats se ancoram nas palavras.**
  - Fora do TTS, vale a rota de transcrição do CLAUDE.md.
  - **Frase recortada de um take longo** (`ref/timeline-vsl.py`): corta no SILÊNCIO medido, nunca numa janela em torno do whisper. A janela pega o rabo da vizinha e ele toca 2×. Coloque um `assert` de que nenhuma amostra do take entra duas vezes.
  - **O fim de frase se confere no espectro** (5–20 ms, chiado × vozeado). O conserto é devolver a sílaba, pegar um s de outra palavra, ou congelar a vogal (num Short do canal, `voz_monta.py`). **Pausa que a montagem da voz abre no apresentador: NUNCA preencher em ida e volta nem de ré** (`<cliente-E>` V2, 29/10: respiração dupla, boca muda e o "pedaço ao contrário" que o autor pegou; o resto sobrou nos 70 s finais). Pausa longa vira cartão em tela cheia; pausa curta vira trecho do bruto com ele calado, pra frente, com corte seco. Antes de entregar, meça no MASTER a sincronia boca×voz do começo ao fim e tire uma tira de cada pausa.
- **Trilha:** Lyria 3.5 pela Gemini API, chave PAGA, sob demanda (`lyria.py`; ElevenLabs = fallback).
  - 2 estilos em paralelo; ganha o que ocupa MENOS a faixa 1–4 kHz.
  - Pede a forma em timestamps; o resto vai pro `bed.py`/`ref/bed-grade.py`.
- **Mix** (`finish.py`, 3 passos em wav): voz com EQ + compressor + `loudnorm I=-16`, chave do `sidechaincompress` da trilha (~14 dB abaixo da fala) · pausas ≥ 2 s: trilha +5 dB · SFX com HP 120 · −14 LUFS, `alimiter 0.841`, pico −1,5 dBFS. ⚠️ Achado aberto (A/B 26/10): o mix sai esmagado (LRA 1,5 contra 4 das refs), mono, sem sub e sem ar. Vem da passada de mix, não do SFX.
- **Legenda** (`caps.py`): texto = o ROTEIRO, tempo = whisper, casados por `difflib` (sem acento, extenso vira algarismo) · ≤4 palavras, sem terminar em palavra-ponte · **cala quando a tela escreve a mesma frase** · palavra falada na cor de acento, **pílula opaca**. Sem legenda (`NOCAP=1` + `finish.py nocap`) só quando o usuário pedir.

## 7. Formatos
- **1:1 (1080²).**
- **16:9:** ensaio do canal (Kitey ou 32Bits) e VSL (UI).
- **Lyric / re-edit de música (9:16 ou 16:9):** `<pasta-de-renders>\lyric-01\LEIA.md` (não incluído). 16:9 aprovado 19/10 = re-edit da `<parceria-musical>`.
- **9:16 (1080×1920):** **texto** no miolo (y 400–1500), legenda na faixa 1520–1700; corpo (aparelho, mascote) cruza faixa, texto não. **Mascote que pula pra baixo de um título CRUZA o texto**: rota pela faixa lateral, desce antes de deslizar (`x = lin²`), título entra depois do pouso.
- **Entrega:** master (nvenc qp 18) + web por `ref/web-vsl.py` (x264 slow crf 21, faststart). O master sai em BT.601 faixa cheia; o web converte pra BT.709 limitada via RGB 16 bits e etiqueta no `setparams`. NVENC no web só pra prévia.

## Pegadinhas (valem pra todos os estilos)
- **Harness:** `sleep` é bloqueado (esperar render por evento de tarefa em background) · log do Python vazio = buffer (`python -u`).
- **DOM:** filho com `visibility:visible` aparece com o pai escondido: `hide` = `visibility:hidden` + `opacity:0` no próprio elemento · texto com `paint-order` revelado só por `fill-opacity` mostra o contorno antes: `stroke-opacity` junto.
- **Lyria:** a duração varia (pediu 1:20, vieram 77,8 e 81,6 s): meça o envelope e ache o acorde final · **`onset_detect` marca o PICO, 30–40 ms depois do golpe:** recue ~.03 s, crossfade de 25 ms antes do golpe, maior salto ≤ p99,9 · `content_blocked` → nome de instrumento/gênero vira descrição · a chave `AQ.…` tem ponto · ⏳ US$0,08/música (10/2026) · licença: **avise, não afirme**.
- **Mix com voz que termina em silêncio digital:** no grafo único a trilha some calada e o `-shortest` come quadros. Por isso são 3 passos em wav; medir o RMS do rabo.
- **Derivado do código envelhece calado:**
  - `events.json` → `sfx.wav` é de quando você rodou. `finish.py` barra `sfx.wav` mais velho que o código.
  - Som listado à mão também: grep do tempo no `EVENTS`.
- **Tiras:**
  - tira pelo ffmpeg com `tile` usa `-frames:v 1`;
  - quadro inteiro escalado, nunca crop.
- **Mídia do acervo:**
  - **Chromakey do Pinterest pode ter pixel não quadrado** (SAR 16:25, 4 de 200): cru, sai 56% mais largo. O `prep.py` da comp-01 endireita.
  - Motion blur pede t < 0 no quadro 0: índice com `%` fica negativo e derruba o render.
- **Custo** (⏳ 09–10/2026, reconferir a partir de 12/2026): 12 s em 1:1 ≈ 1h40 (3 versões + 2 revisões) · 42 s 9:16 com locução ≈ 3 sessões · série 4:5 de ~2 min: 1º ~2,5 h, os seguintes ~35–40 min + render ~15 · VSL de 4 min ≈ 2 sessões, render ~27 min turbo · COMP-01 12 s ≈ 3 h do zero, cena nova com o kit 20–40 min · cada rodada do revisor pega 4 a 9 P1.
