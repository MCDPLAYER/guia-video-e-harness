# Guia de vídeo — lições principais

Resumo do que o autor aprendeu produzindo vídeo e imagem com Claude Code em 2026 (motion por código,
vídeo gerado no Google Flow, imagem estática, áudio/transcrição, roteiro e canal). Cada seção aponta o
arquivo com o detalhe, os números e as datas.

**Como ler:**
- **(preferência do autor)** = gosto, rotina ou cliente dele. Não é regra universal: troque pela sua.
- O resto é aprendizado **medido**: número, data de teste e o porquê ficaram como estavam.
- ⏳ = dado perecível (preço, versão, cota, disponibilidade). Reconfira antes de usar; os testes são de 2026.
- `<cliente-…>`, `<pasta-…>`, `<video-ensaio-NN>` são placeholders de anonimização (ver README).

---

## 1. Princípios que atravessam tudo

- **Vídeo se julga pela MOÇÃO, assistindo, não por frame.** Lendo frames, o Claude escolheu o prompt enxuto;
  no teste cego humano, venceu o detalhado. Em prompt de vídeo, descrever moção, física e câmera rende (a
  regra "prompt enxuto" da imagem estática não vale para vídeo). Divergiu? Vence quem assistiu.
  → `notas/video/feedback_video_julga_mocao_nao_frame.md`
- **Modelo de vídeo que "assiste" erra contagem** (contou pan como corte, errou o número de SFX). Use-o para
  descrever; para contar, use a folha/tira de quadros. → `skills/motion-studio/ref/gramatica-de-edicao.md`
- **Revise o resultado, não o meio.** Em vídeo por código, revisor de arquitetura/segurança/escala é
  desperdício. Só vale achado que termina num número da tela ("texto fica 0,87 s, o piso é 1,2 s").
  `tsc` limpo não prova a peça. → `notas/video/feedback_video_geracao_ia_nao_codigo.md`
- **Revisor às cegas** (sem saber como a peça foi feita, sem explicação de intenção) pega o que quem fez
  não vê. Um brief que explica a peça faz o revisor confirmar o defeito como intenção. Decida pelos achados
  conferidos, não pela nota média (que oscila ~1 ponto). → `skills/motion-studio/ref/REVIEW-PROMPT.md`,
  `skills/peca/ref/REVISOR.md`, `GUIA-HARNESS.md` §5
- **Exit 0 não é prova.** ffmpeg, jobs de IA e scripts falham calados; confira que a saída existe, tem
  tamanho > 0 e é o que você pediu. → `notas/video/reference_ffmpeg_scene_cut.md`

## 2. Motion design por código — `skills/motion-studio/`

- **Quadro = função pura do tempo** (`window.seek(t)`), render com Playwright + Chrome, 4 subquadros por
  quadro (8 em giro rápido) + `tmix` = motion blur real a 60 fps; segmentos de 2 s retomáveis. → `SKILL.md` §4
- **Render: um navegador por worker, nunca abas** (4 abas 55 min → 4 navegadores 27 min); em 4K, ≤ 2
  navegadores em 16 GB; não guardar o PNG da captura (vaza ~33 MB/quadro); NVENC qp 18 ≈ VMAF 98,2 contra
  97,9 do x264 crf 14, com ~45% menos CPU; não misturar encoder no mesmo filme. → `SKILL.md` §4
- **Gates em ordem:** stills (folha) → tiras de 30 quadros a 60 fps de cada transição (os defeitos só
  aparecem aí) → filme → revisor novo às cegas contra um viral do MESMO estilo. → `SKILL.md` §5
- **O modelo forte escreve todo o código e olha o resultado** (POC às cegas: Opus 7,0 · Argon 6,0 ·
  DeepSeek 5,5). → `SKILL.md`, `ref/EM-TESTE.md`
- **Uma peça, um estilo, um arquivo por estilo**, e reabrir as imagens de referência a cada bloco e após
  cada compactação (o minuto que pulou isso saiu sem personalidade). → `SKILL.md` §1, `estilos/*.md`
- **Bancada fixa para comparar modelos** (mesmo prompt, mesmo contrato de render, uma pasta isolada por
  modelo, anotar tempo/chamadas/raciocínio). Pegadinhas: o Codex carrega skills de `~/.agents/skills` (rode
  com skills desligadas); troca de cena que só monta depois do wipe revela tela vazia (adiantar ~0,55 s).
  → `notas/video/BANCADA-MOTION-MODELOS.md`
- **Estilos** (cada um com números medidos de referências):
  - UI/produto: uma forma que nunca corta, máscara nasce da causa, nada > 0,6 s sem evento. → `estilos/ui.md`
  - Vídeo-ensaio "palco" (Kitey): 11–23 cortes/min, plano 1,5–2,1 s, tela cheia ~28% só como respiro,
    música 11–16 dB sob a voz, SFX nunca acima da voz. → `estilos/kitey.md`
  - Colagem pop (32Bits): corte ≠ composição (34 cortes/min, ≤ 1 composição nova por frase); cor viva
    30–40% da tela medida por script. → `estilos/32bits.md`, `estilos/32bits-cardapio.md`
  - Papel/stop-motion: pose de 2 quadros com ±25–30 ms; tremor só na peça que se mexeu; tremor global dá
    dor de cabeça. → `estilos/papel.md`
  - Vidro só se lê em movimento sobre detalhe; fundo do shader em data URL. → `estilos/vidro.md`
- **Transições de shader:** o rabo do ease-out deixa lasca de 5–6 quadros → progresso ×1,12–1,3 com teto;
  1–2 materiais por peça (10 = mostruário, perdeu em coesão). → `ref/shaders-lib/LEIA.md`
- **Clipe gerado por IA dentro do motion:** o modelo de vídeo desfaz o movimento no meio → folha a cada
  0,5 s e usar só o trecho monotônico. → `ref/ASSETS-GERADOS.md`
- **Áudio do motion:** SFX de banco real ganhou do sintético (0,85 × 0,14); alinhar pelo PICO (onset marca
  30–40 ms depois do golpe); recortar frase de take longo no silêncio medido. → `SKILL.md` §6
- **Laboratório:** ideia de fora entra como versão B com UM experimento e só é promovida depois de ganhar
  numa peça real. → `ref/EM-TESTE.md`
- (preferência do autor) locução + trilha em toda peça; densidade de SFX pela régua dele; `modo=leve` porque
  usa o PC durante o render.

### Reels e VSL de produto (lições de storyboards de cliente que saíram do repo)
- **Reels 9:16 (~42 s) com uma forma única:** o objeto central do produto atravessa a peça e VIRA cada
  estado; cada lugar da história = uma cor de fundo; quadro final = quadro 0 (loop); três famílias de tipo
  com papel fixo e um acento único; os dados batem de ponta a ponta. O revisor às cegas encurtou um vazio de
  0,9 s → 0,2 s e trocou 3 s de câmera parada por um passo de câmera por palavra-chave.
- **VSL 16:9 (~4 min):** um mundo, uma câmera, nenhum corte; estações montam quando a câmera chega e
  desmontam quando sai; até ~12 s de voz e depois um respiro de 2–3,4 s em que a imagem "paga" o que foi dito;
  no respiro a música manda (pico no quadro da batida). Verdade: preço exato, demo marcada "DADOS DE EXEMPLO",
  nada de depoimento inventado ou "% garantido".
→ detalhes técnicos em `skills/motion-studio/estilos/ui.md`

## 3. Gramática de edição e vídeo-ensaio — `skills/motion-studio/ref/gramatica-de-edicao.md`

- **Ritmo certo não salva quadro pobre.** Uma intro "só ok" estava DENTRO da faixa de 3 criadores de
  referência em cortes/min, plano mediano e música sob a voz. Faltava composição: quadro construído (o clipe
  como objeto numa cena), cor da peça, textura, texto como cenário, quebra de registro a cada 30–40 s, trilha
  que não incha, SFX gravado. Mesma locução e mesmos clipes, só mudando a composição → aprovado.
- **Cardápio (não gate):** corte seco é o padrão, transição só com motivo que caiba numa palavra; zoom com
  razão; todo elemento com função; respiro depois de trecho denso; o ouvido confirma o olho.
- **Retornos e consertos medidos (v2 → v3.2 de um ensaio):** "SFX uma mesmice" → 119 sons de 59 arquivos;
  "música tímida" → envelope em onda; "a câmera voa e para" → deriva contínua (tempo parado 24,5 s → 3,0 s);
  "SFX altos" → −5 dB e o golpe mais alto 2,7 dB abaixo da voz. → também `estilos/kitey.md`
- **Gap em aberto:** o visual segue o TEXTO da transcrição, não o TOM da voz. Hipótese não medida: marcar o
  tom por trecho e casar a moção.

## 4. Vídeo gerado no Google Flow / Omni — `skills/gflow/`

⏳ Tudo aqui é de 2026 e o Flow muda rápido. O changelog (`ref/changelog.md`) tem as datas de cada achado.
- **Preço do Omni (medido 27/08/2026):** 720p x1 = 4 s 7 · 6 s 10 · 8 s 12 · 10 s 15 créditos; 360p = metade;
  video edit = +67%; fatiar custa mais (2×4 s = 14 > 1×8 s = 12). Fórmula:
  `base × variantes × (0,5 se 360p) × (1,67 se video edit)`. → `SKILL.md` (painel de vídeo)
- **Operar pela UI com navegador controlado pelo Claude:** clique real (`.click()` por JS não envia); ler
  "A geração vai usar N créditos" antes; editar imagem SOBRESCREVE o asset; depois de video edit o download
  entrega a versão ANTERIOR até selecionar a última miniatura; `UNUSUAL_ACTIVITY` = parar. → `SKILL.md`
- **Nunca pedir silêncio/mudo no prompt** (o filtro de vídeo silenciado derruba o take); escreva "quiet
  natural room ambience, no music, no voices".
- **Texto:** bloco de texto na tela, um rótulo por vez; texto grande e telefone sobrevivem, miúdo derrete;
  número preciso em moção derrete → overlay. Card com texto em PT vai para outro gerador.
- **Fala:** bloco de voz obrigatório em multi-take (senão cada take sorteia uma voz); ~5 sílabas/s;
  escrever com acento; casar duração com a fala.
- **Video edit de take real re-dubla a fala por padrão:** travar áudio/pessoa/fundo + transcrição exata;
  fallback `ffmpeg -map 0:v -map 1:a`.
- **Moderação:** 1 variável por vez com um take aprovado como controle; não é composicional; fechamento com
  escassez/WhatsApp na fala barrou várias vezes (padrão observado, não gatilho provado) → vai para selo/cartela.
- **Quota de imagem ≈ 97–100 numa janela deslizante de ~24 h** (não zera à meia-noite; medido 15→16/07).
- **"Comando em barra" é folclore de criador:** sintaxe mágica só depois de A/B com controle.
- **Lugar real como cenário:** 1 foto de referência + 3–4 traços visuais descritos segura o marco; o
  grounding por mapas era só EUA (⏳).
- (preferência do autor) Omni como padrão de vídeo; vídeo só com OK e custo lido na UI; SFX e música
  sempre no editor; look natural/UGC.
- **Efeitos no Omni:** prompt "sutil/translúcido" faz o efeito sumir; cada negativa ("no X") precisa de um
  defeito observado, senão sai; cautela é para gasto e publicação, não para estética.
  (preferência do autor: default ambicioso, 1 efeito por take) → `notas/video/feedback_efeitos_omni_ambicioso.md`

### Automação headless — lições do caminho que morreu (arquivo apagado)
O autor rodou o Flow sem tocar na UI de 02/06 a 23/09/2026, por um agent local + extensão do Chrome que
pegava o token da aba logada. Em 09/09 o Flow migrou de domínio e passou a marcar como sequestro todo token
de reCAPTCHA pedido por extensão. Contornar isso é burlar anti-bot: **não se faz**. O arquivo foi apagado;
o que vale para qualquer automação em cima de app web de consumo:
1. **Endpoint privado é tempo emprestado.** Em 3 meses o alvo mudou três vezes sem aviso. Mantenha a rota
   pela UI documentada como caminho de volta; quando o alvo ligar anti-bot, pare.
2. **Linha do tempo antes do conserto conhecido:** último sucesso REAL (artefato com timestamp) → primeira
   falha → o que mudou naquele minuto. Por 14 dias a queda foi tratada como "mais uma quebra semanal".
   (método completo em `notas/harness/feedback_fix_root_cause.md`)
3. **O health check mente:** voltava "conectado" com a aba morta (token em cache). A prova de vida é UMA
   operação real e barata, nunca o health.
4. **Erro de ESTADO é fatal: aborte no primeiro, não repita.** Retry com 401 abriu até 14 abas no Chrome
   do usuário; retry não conserta estado, só multiplica o estrago.
5. **Regra crítica no meio de uma lista longa não é procedimento:** vira passo de abertura no topo.
6. **"Falhou" mas cobrou crédito → suspeite do output.** Takes "com timeout" estavam prontos no projeto e
   cobrados; recupere pelo id/UI antes de regerar.
7. **Não chute nome de campo de API privada:** gere 1 vez pela UI com a feature ligada e capture o pedido
   real. Wrapper headless congela você nas features da época em que foi mapeado.
8. **Esperar estado, não tempo** (`sleep` fixo de 3 s quebrou quando o carregamento ficou mais lento).
9. **Lote precisa de idempotência** (pular output existente) e de parear saída pelo id do pedido, não pela
   duração; confira as N saídas por transcrição contra a fala esperada.
10. **Windows:** `requests` com `json=` estragou acento (use `json.dumps().encode()`); `json.load(open())`
    lê cp1252 (passe `encoding="utf-8"`); o curl do git-bash precisa de caminho Windows no `-F`.

## 5. Imagem estática / peça de cliente — `skills/peca/`

- **Método curto:** carta do cliente (1 página, ≤ 60 linhas) + ficha de fatos com fonte + vitrine das
  últimas peças + 1 revisor às cegas. Medido 29–30/09: 8–11 min por peça contra 23–30 min no caminho
  anterior (~77 mil tokens de regra antes do 1º prompt). → `SKILL.md`, `ref/CARTA-MODELO.md`
- **Revisor às cegas bloqueia só em 2 casos:** afirmação sem ficha e "não entendi o que a peça vende".
  → `ref/REVISOR.md`
- **ChatGPT Images (⏳ medido set–out/2026):** thread nova por peça; não escreva bloco de formato; texto
  literal entre aspas e já acentuado; prompt curto faz o modelo INVENTAR blocos de texto; papel em cena volta
  com dado inventado; logo anexada à parte (9/11 fiéis). → `ref/chatgpt-images.md`
- **Foto realista no Flow/Nano Banana:** prosa corrida (rótulos por região viram imagens separadas); texto
  em PT instável → card com texto vai no ChatGPT; logo pequena emborra. → `ref/foto-flow.md`
- **Regra que só adiciona polui canvas limitado** (cards foram de 3 para 7+ blocos). Ver `GUIA-HARNESS.md` §2.

## 6. Áudio, transcrição e ffmpeg — `notas/video/`, `skills/media-tools-local/`

- **Corte por cena:** `select='gt(scene,0.2)'` + mínimo 1,5 s entre cortes; segment muxer, 1 ffmpeg por
  fonte; `-forced-idr 1` obrigatório com nvenc (sem ele, 16 clips em vez de 37); detectar no original.
  → `reference_ffmpeg_scene_cut.md`
- **ffmpeg calado:** `-ss` antes do `-i` em JPEG sai com exit 0 e não grava nada; `-ss 1` em clip < 1 s dá 0
  frames; `subprocess(text=True)` no Windows decodifica em cp1252 e morre sem log. → idem
- **Transcrição para cortar:** SRT pronto vence ASR; Whisper na nuvem pode não dar tempo por palavra; um
  modelo apagou nome próprio recorrente em arquivo de 24 min (0× contra 15×) → picotar em 3–5 min com ~10 s
  de sobreposição; `silencedetect` com `-v error` devolve zero linhas; `condition_on_previous_text=False` em
  retake; um motor sozinho não prova que a fala foi comida. → `reference_transcricao_timestamps.md`
- **Ler um template de vídeo com números:** crop por variância, `scdet` para cortes, optical flow para
  zoom/pan, processar em streaming (518 frames 1080×1920 deram ~6,5 GB de pico). → `reference_ler_template_video.md`
- **TTS do Gemini (⏳):** 107 vozes pt-BR sem sotaque regional; tags de riso funcionam em PT; voz clonada só
  existe no projeto da chave que a criou; clonar na chave free devolve 500, não 403. Usar voz clonada é
  decisão do dono da voz. → `reference_gemini_tts.md`
- **Toolchain local:** nunca modelo `small` para fala de cliente; corte de silêncio −30 dB / 0,2 s / pad
  0,05. (preferência do autor: transcrição na nuvem por padrão, pela velocidade) → `skills/media-tools-local/SKILL.md`

## 7. Roteiro e canal — `skills/roteiro/`, `skills/canal-youtube/`

As duas skills são moldadas para o canal do autor (ensaios de opinião, nicho anime/cultura pop).
Reaproveite o **método**; tom, nicho, gírias e preferências são dele.
- **Fala falada ≠ texto escrito, o vício nº 1 da IA:** nada de lista staccato, travessão de aposto, frase
  nominal; use conectivo de oralidade; faça o teste da boca antes de entregar. → `roteiro/SKILL.md`
- **"Não é X, é Y": a origem decide.** O tique é da IA; texto do humano não entra na conta. → idem
- **O crítico tem viés contra a voz:** otimiza "nada falso passou", nunca "a voz sobreviveu". Num teste de
  8 achados, o autor venceu 5. Aplique item a item e deixe o humano desempatar. → idem
- **Estrutura de ensaio:** tese em camadas; validador "o que o espectador ainda NÃO sabe depois deste
  trecho?"; um clímax só; hook ≠ tese e nunca mente em fato verificável. → idem
- **Duração por caracteres:** 1.000–1.200 caracteres/min no ritmo do autor (15 min ≈ 16.500). → `canal-youtube/SKILL.md`

### Analytics de canal pequeno (lição de um arquivo de números que saiu do repo)
- Separe princípio de número: o número mora num snapshot DATADO ("⏳ reconferir no YouTube Studio").
- **Canal de unicórnios:** 3 vídeos tinham mais da metade de todas as views; média vira outlier disfarçado.
  Uma "regra" de título (negação > pergunta) era UM vídeo puxando a média (1 sucesso + 1 médio + 8 fracassos).
  Só afirme padrão com N e mediana na mão.
- **CTR:** olhe a dispersão (variava > 10× entre vídeos); o gargalo era volume de impressões.
- **% de retenção esconde minutos por view:** o vídeo mais longo, com a menor %, entregava quase o dobro de
  minutos por view. Escolha a duração pelo objetivo (longo entrega minutos, curto/pessoal converte inscrito).
- Conversão com N = 1 é hipótese, não lei. Confira ranking contra a própria tabela.
- Meça cada etapa do funil por plataforma: o gargalo muda (alcance → visita → seguidor).

### Conteúdo curto e parceria (de outro arquivo que saiu)
- Parceria não é canal secundário: pasta, formato e destino próprios.
- Um corte, vários destinos (corte ≤ 60 s → legenda traduzida → perfil do parceiro; mesmo corte, outro
  layout, para shorts/reels).
- (preferência do autor) curto = gancho em 2–3 s, tese antes de 10 s, 1–2 argumentos, virada no fim;
  45–90 s para opinião. Adaptar longo → curto é reescrever, não comprimir: o curto tem que ser autônomo.

## 8. Limpeza de disco sem perder nada — `skills/limpar/`

- **Três portas:** arquiva / Lixeira / holding; na dúvida, holding. Mover e deletar em passos separados
  (misturar já custou 3 arquivos). Gate por hash, conferido na hora: nome, tamanho ou similaridade ~1,0 não
  bastam. → `SKILL.md`
- **Texto não tem hash contra mídia:** contenção linha a linha; nota que "afirma backup" não é backup.
  → `references/gate-texto-e-ponteiros.md`
- **"Subiu para o Drive" precisa de prova na API** (título + tamanho + data de hoje). → `references/rotinas-disco.md`
- **Casar arquivo com vídeo publicado pela DURAÇÃO (±1,5 s), não pelo título** (o casamento ingênuo marcou
  368 de 427 como publicados; pelo número da parte, 271). → idem
- (preferência do autor) as frentes, pastas e a rotina de quando limpar.
