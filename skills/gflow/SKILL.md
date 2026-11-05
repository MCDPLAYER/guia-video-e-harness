---
name: gflow
description: "Mapa vivo do Google Flow e metodologia Omni/Agent. Use SEMPRE que o usuário mencionar Flow, gflow, Gemini Omni, Omni Flash, Flow Agent, storyboard-to-video, ingredientes no Flow, avatar, end frame / frame final, rascunho 360p, export 1080p/4K, validação de outputs, Nano Banana/Veo dentro do Flow, ou pipeline de vídeo/imagem no Flow. Mapa de capacidades, prompts e workflow. Desde 29/09/2026, a operação do Flow pelo Claude é pela UI no NAVEGADOR INTERNO do app Claude (mcp__Claude_Browser__*; login persiste só com Keep cookies = Shared no menu ⋮ do painel; Claude in Chrome só como fallback), em flow.google.com: upload de imagem/vídeo como ref, geração e download, usando a quota Pro da conta Google do usuário. Havia um caminho headless (agent local + extensão do Chrome) que morreu em 23/09/2026: não subir (ver GUIA-VIDEO.md)."
---

# gflow — Mapa Flow/Omni e metodologia de produção

## 🪦 Havia um caminho headless que morreu em 23/09/2026 — NÃO subir agent nem mandar request

> Um agent local + extensão do Chrome dirigia o Flow por HTTP. O Flow migrou pra `flow.google.com` e passou a marcar como `extension_hijack_detected` todo token de reCAPTCHA pedido por extensão (volta `[7] PUBLIC_ERROR_UNUSUAL_ACTIVITY`). Contornar isso = burlar anti-bot: **não se faz**, e cada tentativa é mais um sinal contra a conta Google. Ver GUIA-VIDEO.md. Mapa de capacidades, prompts e método valem; a operação é só pela ROTA VIVA abaixo.

## ✅ ROTA VIVA (DEFAULT desde 29/09/2026; o autor achou *"mt mais pratico"*): Flow pelo NAVEGADOR INTERNO do app Claude (`mcp__Claude_Browser__*`)

> O login só **persiste** se o menu **⋮** do painel do navegador estiver em **Keep cookies = Shared** (o autor ligou em 19/10: Cowork e todas as sessões do Code compartilham os cookies). O padrão das sessões do Code é **apagar os cookies quando o app fecha** (no log aparece `preview-static-partition-wipe`), e aí cada abertura ou update pede o login de novo (medido em 19/10). Pediu login no meio do lote → paro e aviso o usuário, nunca digito senha. O **Claude in Chrome** (`mcp__claude-in-chrome__*`) virou **fallback**: ele pode estar ligado ao Chrome de **outra pessoa que usa a mesma conta Claude** (no caso do autor, o titular da conta), e aí o Flow devolve **404 / projeto sumido** (conta Google errada). **Só usar o Chrome se o usuário pedir.**

O reCAPTCHA avalia a sessão normalmente, sem burla: se reprovar, falhou e **não repete**. Passos medidos (29/09, peça do `<cliente-farmácia>`):
1. **Aba:** `navigate`/`preview_start {url}` em `https://flow.google.com/project/<id>` (projeto novo pelo "+" da home). Ler com `read_page`/`find`/`get_page_text`/JS. `zoom` não é suportado e o screenshot às vezes estoura o tempo. Viewport emulado (`resize_window`) volta pro `desktop` no fim.
2. **Upload de ref (imagem ou vídeo, ≤10 MB):** ⛔ **NUNCA clicar em "Enviar"** (abre o seletor nativo do Windows). Não existe `file_upload` aqui: o arquivo entra por JS, em pedaços de base64 → monta o `File` → confere o SHA-256 no browser contra o local → `DragEvent` (`dragenter`/`dragover`/`drop`, bubbles) em `.tile-row` (grade vazia: `.tiles-container`). **Ref que mora no Google (foto 360 / Street View do Maps) dispensa o base64:** `fetch` da URL `lh3…` direto na aba do Flow, recorte e marca d'água apagada num OffscreenCanvas, drop (changelog 03/11). O localhost é bloqueado pelo app; não contornar. Aviso "Direitos de uso" de vídeo: **quem aceita é o usuário**.
3. **Anexar ref no prompt:** `Adicionar elementos à caixa de comando` → clicar na linha pelo nome (às vezes já inclui; senão `Incluir no comando`). Vira chip no campo.
4. **Configurações:** `Gatilho de configurações` → `read_page filter=interactive` lista os radios (Imagem/Vídeo · Frames/Elementos · 16:9/9:16 · 360p/720p · 4s/6s/8s/10s · x1–x4). O "4s" não aparece no `find` por texto, e os refs mudam a cada abertura. O default pode vir **16:9**. **Ler "A geração vai usar N créditos" ANTES de enviar.** Imagem: `Nano Banana 2 Lite` = 0 · `Nano Banana 2` = 0 · `Nano Banana Pro`. Vídeo: Omni 1.1 Flash 720p x1 = 4s 7 · 6s 10 · 8s 12 · 10s 15 → **vídeo só com OK do usuário**.
5. **Frames (vídeo com frame inicial/final):** "Início"/"Fim" abrem um seletor **sem miniatura carregada**; escolher pelo TÍTULO do asset. Um clique no item JÁ inclui o frame. ⚠️ **Editar uma imagem no Flow SOBRESCREVE o asset**: o frame de uma série sai do **upload do arquivo local**, nunca do tile editado. **Encadear take sem upload:** abro o take, pauso o `video` por JS em `duration-0.05`, clique real em `Salvar frame` → aviso "Frame salvo" → o quadro vira imagem nova na grade do projeto e entra como "Início" do próximo take (`<cliente-app>`, 14/10).
6. **Prompt + enviar:** `.focus()` no `[contenteditable=true]` → `computer type` → **clique real** (`computer left_click` pelo ref) em `Iniciar geração`. `.click()` por JS NÃO envia.
7. **Resultado:** "Falha / Não foi possível gerar" = falhou, sem cobrança. O tile novo aparece na grade.
8. **Baixar:** tile → ícone de download (clique por REF, coordenada não grava) → imagem `1K` / vídeo `720p Tamanho original`. ⚠️ **Depois de um video edit, o download segue entregando a versão ANTERIOR**: no editor, em "Mostrar histórico", clique na última `img[alt="Miniatura do vídeo gerada"]` (dispatch de pointer por JS) e só então baixe. Confira com `cmp` contra o cru (03/11, 3 casos; um reenvio pago à toa). Pra refazer um efeito a partir do take cru, clique na PRIMEIRA miniatura antes de digitar. Cai em `<pasta-do-usuario>/Downloads`: imagem vem como `<GUID>.tmp` (JPEG inteiro, renomear), vídeo com o nome real. Mover pro `<pasta-de-midia>/flow-out/<frente>/`, conferir, e a cópia de Downloads vai pra Lixeira depois do hash.
- **Teste de vida antes de lote:** 1 imagem `Nano Banana 2 Lite` 9:16 com 1 ref → tile novo + download. Aborta no 1º erro.
- **Erros:** `[8]` (RESOURCE_EXHAUSTED) = cota diária/modelo sem tráfego → troca pro Lite. `[7] UNUSUAL_ACTIVITY` = para tudo e avisa o usuário.
- **Diálogo entre 2 personagens no mesmo quadro:** **1 fala por take**, os dois sempre em cena, e no prompt *"Only X speaks. Y keeps her mouth closed the whole time"*. Funcionou em 4 de 4 takes no 720p (29/09). Duas falas no MESMO take ainda não foi testado: rode 360p antes. Defeito no fim do take (objeto que some) se conserta com **punch-in na montagem**, sem re-rolar.

### Fallback: Claude in Chrome (`mcp__claude-in-chrome__*`), só se o usuário pedir
Os passos são os mesmos. As diferenças:
- O upload tem `file_upload`: JS cria `<input type=file>` → `file_upload(ref, paths)` → `DataTransfer` + drop em `.tile-row`.
- O download cai no **Desktop**, como `<Título>_<AAAAMMDDhhmmss>.jpeg`.
- Aba em segundo plano devolve print velho, e Chrome minimizado congela o print: restaure a janela em vez de repetir o screenshot.
- **Antes de qualquer ação: aperto de mão.** Só o `onThisComputer`, `select_browser` pelo id, prova com `peca\ref\chrome-qual-navegador.ps1` (script não incluído); sem prova, pare e avise.

## Notas relacionadas (abrir só se o tema pedir)

- [[project_gflow]] (nota não incluída) — mestre do Flow (Omni-first).
- [[reference_flow_maps_grounding]] (nota não incluída) — Flow Maps ancora em Street View REAL, mas só EUA. Não prometer grounding de rua brasileira. ⏳ reconferir a disponibilidade.
- [[feedback_omni_video_edit_lote]] (nota não incluída) — em LOTE, `bg-only` > produto inventado.
- [[reference_omni_api_oficial]] (nota não incluída) — API oficial `gemini-omni-flash-preview` é PAGA e RESPEITA a ref.
- [[reference_remover_marca_flow]] (nota não incluída) — delogo da marca ✦ funciona a 720p; fica invisível só em fundo liso.

## 🎛️ PAINEL DE VÍDEO — as 4 escolhas do Omni 1.1 (CANÔNICO · 27/08/2026)

> **Tudo que decide formato e preço de vídeo mora AQUI.** §Durações, §Resolução e §Quota apontam pra este bloco; se algo divergir, **este manda**. Medido nos prints da UI do autor (não é anúncio).

**1. MODALIDADE — define o preço E se você escolhe a duração**

| Aba · input | O que é | Duração | Preço |
|---|---|---|---|
| **Frames** · imagem | frame inicial **+ END FRAME** (🆕 1.1) | você escolhe | tabela base |
| **Elementos** · imagem | i2v / r2v por referência (ELEMENTOS) | você escolhe | tabela base — **IGUAL à Frames** |
| **Elementos** · vídeo | **video edit** (conserta take real) | ❗ **travada pelo vídeo-fonte** | **+67%** |

🔴 **O que encarece é o TIPO DE INPUT, não a aba.** Imagem custa igual como frame ou como elemento. Quem cobra mais é **vídeo de entrada**.

**2. RESOLUÇÃO — só existem `360p` e `720p` na geração.** 360p = **metade** do preço. **1080p / 4K é EXPORT** (upscale em cima do 720p já gerado), **não é modo de geração** ⇒ o nativo do Omni é 720p, e entrega de cliente em 1080p tem um passo a mais.

**3. DURAÇÃO — 4s / 6s / 8s / 10s.** ⚠️ **Não é proporcional: fatiar sai mais caro.** 4s custa 1,75 créd/s e 10s custa 1,5 ⇒ **2 takes de 4s = 14** contra **12 de um único 8s**. Decupagem virou decisão de orçamento.

**4. VARIANTES — x1 a x4, multiplicador LINEAR** (10s 720p x4 = 60 = 15×4).

#### 💰 Tabela base — 720p · x1 · input de texto ou IMAGEM · ⏳ medida em 27/08/2026

| 4s | 6s | 8s | 10s |
|---|---|---|---|
| **7** | **10** | **12** | **15** |

**Fórmula de orçamento:** `base(duração) × variantes × (0,5 se 360p) × (1,67 se video edit)`
> Ex.: reel de 3 takes de 8s, 720p, x1 = **36 créd**. Rascunhar os 3 antes em 360p = **+18**. Video edit de um take de 8s = **20**.

🪦 **NÃO EXISTE MAIS PISTA DE 0 CRÉDITO.** O `[Lower Priority]` **acabou no Flow** (confirmado pelo autor, 27/08/2026). O mais barato que existe hoje é **360p — que é metade, não zero**. Todo teste de vídeo custa crédito; planejar lote assumindo tier grátis é erro de orçamento.

#### Decisão rápida

- **Direção/moção incerta** → roda **360p** primeiro, julga a MOÇÃO (não o frame), só então gasta o 720p.
- **A cena precisa TERMINAR num lugar específico** (produto na mão, olhar pra câmera, CTA entrando) → **Frames com end frame**. Ver §Método Omni.
- **Já existe take real pra consertar** → **video edit**, ciente do +67% e de que a duração vem da fonte (pra mudar, cortar a ENTRADA).
- **Entrega de cliente** → gera 720p e **exporta 1080p**.
- **Take longo > vários curtos** quando a cena aguentar — é mais barato por segundo.

## 🎥 Controle de LOOK — natural/UGC ("gravado no celular") vs cinemático
Descoberto 2026-06-02 — o autor aprovou a vibe **natural/UGC** de um vídeo do `<cliente-ótica>` ("ficou tão gravado por telefone, não aquele cinemático pesado"; a escolha do look é preferência do autor, o controle abaixo é técnica). **No r2v a REF manda no look** (luz/grão/estética): o vídeo HERDA a imagem base — controlar o look é tarefa da ref, não do prompt do vídeo.
- **Natural / UGC / "telefone"** (o que o autor curtiu): ref com `bright airy/even lighting` + `clean/minimalist` + `photorealistic`; vídeo com `locked-off shot` + `natural ambience, no background music` + `subtle natural gestures`. **A omissão é o segredo** — NÃO usar termos de cinema.
- **Cinemático pesado**: ligar os gatilhos — `cinematic`, `dramatic/moody lighting`, `shallow depth of field/bokeh`, `film grain`, `color grade`, `push-in/dolly`, + trilha sonora.
- **Trilha é o que mais "cinematiza"** — `no background music` sozinho já derruba a maior parte do peso de produção. Luz clara+uniforme na ref + câmera travada + sem música + zero termo de cinema = vibe celular.

## Economia (regra dura)
Imagem custa 0 crédito; vídeo consome (tabela no §PAINEL). Vídeo só sob pedido; diga o custo lido na UI antes de disparar; lote = pergunte (preferência do autor, que paga a quota).

---

## Papel da skill

Mapear o que Flow/Omni/Agent fazem, transformar brief e refs em prompts, desenhar a produção em takes de até 10s e operar a UI pelo navegador interno (ROTA VIVA). Claude in Chrome é só fallback.

**Combina com motion por código** (menu, não regra; autor, 30/09): quando a fala do talking head entra em dado ou relação entre números, o motion (`motion-studio`) entra por cima ou intercalado, em vez de regerar a pessoa até o gráfico sair certo. O eixo de cada ferramenta está em `motion-studio\ref\ASSETS-GERADOS.md`.

## Pré-requisitos

Login Google do usuário no navegador interno, em flow.google.com. Antes de lote de vídeo, ler o saldo na conta e o aviso "A geração vai usar N créditos".

---

## Modelo padrão: Gemini Omni Flash (**1.1** desde 27/08/2026)

**A partir de 22/05/2026, Omni Flash é o padrão de vídeo no Flow do autor** (escolha dele, pelos motivos medidos abaixo).

🔴 **27/08/2026 — o Omni Flash 1.0 NÃO EXISTE MAIS no Flow.** O dropdown de vídeo hoje tem exatamente 4 opções: **Omni Flash 1.1** · Veo 3.1 Lite · Veo 3.1 Fast · Veo 3.1 Quality. Não há fallback pro 1.0 — "Omni Flash" neste arquivo = 1.1.

🆕 **27/08/2026 — Omni 1.1 Flash.** Somou três coisas, todas conferidas pelo autor na UI: **end frame** (fixa o frame final, não só o inicial), **360p** pra rascunho barato e **export 1080p/4K** por upscale do 720p. Detalhe e pegadinhas no bloco §PAINEL DE VÍDEO.

Motivos observados pelo autor:
- Mais realismo que Veo 3.1 nos testes práticos.
- Cerca de um terço do custo percebido em relação ao Veo.
- 10 segundos por clipe no Flow.
- Multimodalidade real: aceita vídeo, imagens, ingredientes, frames e avatar como referência. A página oficial do DeepMind também cita texto, imagem, vídeo e áudio como referências; input de áudio no Flow ainda precisa ser testado pelo autor antes de virar promessa operacional.
- Edita vídeo nativamente por conversa, mantendo coerência de cena entre passos.
- Transforma storyboards/desenhos em vídeo realista, usando o desenho como guia de movimento/estrutura.

Avatar do autor:
- Rosto ficou excelente.
- Voz ficou ruim; não usar como voz final sem reteste.

Fontes oficiais úteis:
- `https://deepmind.google/models/gemini-omni/`
- `https://deepmind.google/models/gemini-omni/prompt-guide/`

## Checklist de capacidades Omni

Capacidades confirmadas nas páginas oficiais DeepMind, com observações do autor no Flow quando houver:

- **Vídeo por conversa natural:** editar qualquer vídeo passo a passo, preservando a cena entre emendas.
- **Multi-turn consistente:** cada edit constrói sobre o anterior; bom para trocar ambiente, objeto, câmera e ação sem recomeçar.
- **Input multimodal:** texto, imagem, vídeo e áudio como referências; no Flow, áudio ainda precisa de teste prático do autor antes de prometer como workflow.
- **Combinação de inputs:** misturar vídeo + imagem + áudio + texto em um output narrativo único.
- **Transformação estética:** aplicar estilos como anime, claymation, watercolor, crayon, graphite, glass, risograph, voxel, felt, hologram, line art etc.
- **Reimaginar ação:** mudar o que acontece no vídeo, inclusive efeitos físicos e interações.
- **Sincronia com som/música:** sincronizar luzes, movimento, toques ou objetos com beat/trilha/som.
- **Gerar som contextual:** exemplos oficiais incluem sons realistas, sons de animais, harp sounds e música de fundo; no Flow, testar caso a caso.
- **Editar vídeos usando imagens:** usar imagem como referência para objeto, arquitetura, estilo, material ou personagem.
- **Trocar personagens/objetos:** substituir personagem/objeto por referência, mantendo cena coerente; guia oficial cita personagem novo seguindo motion/dialogue.
- **Transferência de movimento:** aplicar pose/motion de um vídeo a personagem/imagem, ou transferir movimento abstrato de um elemento para outro.
- **Transferência de material/estilo:** aplicar textura/material/estética de imagem a objeto ou cena.
- 🆕 **End frame (1.1, 27/08/2026):** definir o frame **final** do take além do inicial — fecha o take num destino conhecido em vez de torcer pro movimento parar no lugar certo. UI desktop.
- 🆕 **Rascunho em 360p (1.1):** metade do preço do 720p.
- 🆕 **Export 1080p/4K (1.1):** upscale das gerações 720p — o nativo é 720p, o 1080p é passo de saída.
- **Storyboard/sketch para vídeo:** transformar storyboard, desenho ou doodle em footage realista usando o desenho como guia de movimento, sem mostrar o desenho final.
- **Consistência de cena:** manter personagem, objeto e ambiente consistentes usando referências reais ou geradas no Nano Banana.
- **Texto em vídeo:** renderizar texto melhor e sincronizar texto com a ação; controlar tipo, posição, animação e exposição.
- **Controle de câmera:** close, medium, wide, over-the-shoulder, point of view, static/locked/fixed, push in, punch in, dolly zoom, smartphone zoom, film camera, webcam style.
- **Avatar como referência:** avatar do autor funcionou bem para rosto, ruim para voz; usar como referência visual, não voz final.
- **Superfícies:** Gemini app, Google Flow e YouTube Shorts; recursos variam por tier e geografia.
- **Transparência:** outputs criados/editados no Gemini app, Google Flow ou YouTube incluem SynthID imperceptível e C2PA Content Credentials.

---

## Flow Agent: uso certo

O Agent do Flow é fraco como "inteligência criativa geral". Não confiar nele para estratégia, roteiro, julgamento estético ou decisão de produção.

Ele é útil em duas coisas:
- Ler um brief natural e transformar em prompt funcional para o Flow/Omni.
- Ler nativamente outputs de imagem/vídeo dentro do Flow e comparar com o pedido.

Método recomendado:
1. Claude/Codex/usuário define o objetivo, estética, continuidade e lista de takes.
2. Agent recebe um brief curto e natural por take.
3. Agent converte para prompt funcional do Omni.
4. Omni gera/edita o take.
5. Agent faz uma validação objetiva: bateu com brief? preservou personagem? respeitou movimento? há artefatos?
6. Usuário/Claude decide criativamente o que aceitar, refazer ou segmentar.

Regra: Agent é tradutor e inspetor, não diretor.

---

## Método Omni para storyboard → vídeo

Para vídeos maiores, não pedir "um vídeo inteiro". Quebrar em takes.

1. Criar storyboard visual ou lista de beats **com o ELO de cada corte**. Pra cada passagem
   take→take, escreva o que leva de um ao outro e onde ele aparece. **Em ordem de preferência:**
   **NA CENA**, com a causa acontecendo DENTRO de um take: frame inicial = antes, frame final =
   depois (luz acesa → apagada, aparelho desligado → ligado), um lugar por take. Depois **NA FALA**,
   **NO SOM** (áudio do plano anterior seguindo por baixo, J/L-cut) e, por último, **still/overlay**.
   Elo que só existe no roteiro é um beat que falta.
   🔴 **Still em sequência pra contar a causa vira slideshow (preferência do autor, 25/09).** Conte a causa com end
   frame (frame inicial = antes, frame final = depois), não com stills. Economizar crédito não
   decide a forma da peça: um teste de 4s é barato (tabela no §PAINEL).
   🔴 **ANIMATIC antes de crédito (autor, 25/09).** Monte os frames na ordem, com os stills e overlays de
   custo zero no lugar dos takes e a fala escrita, e passe pela leitura nua (`peca`, revisor em
   `peca\ref\REVISOR.md`, vídeo). Só depois se renderiza. **A revisão é da SEQUÊNCIA, não de cada take solto.**
2. Dividir em takes de até 10s. **A fala manda na duração, não o contrário:** conte ~40 sílabas por
   10s (o Omni fala ~5 sílabas/s e às vezes inventa uma palavra de abertura). Não coube? Divide em
   mais takes. Não comprime a frase.
3. Para cada take, anexar referências necessárias: frame inicial/final, imagem de personagem/objeto, vídeo de movimento, storyboard, avatar.
4. Promptar com:
   - ação principal;
   - o que deve permanecer igual;
   - câmera;
   - som/música, se a UI aceitar (⚠️ nunca pedir silêncio/mudo — ver bloco abaixo);
   - restrições explícitas ("sem texto", "sem música", "não mostrar o desenho no final").
5. Usar multi-turn para ajustes localizados, não refazer tudo de primeira.
6. Encadear takes por continuidade **visual E causal**. Visual: o último frame do take anterior vira
   referência do próximo. Causal: o próximo começa pelo que o anterior deixou em aberto (a pergunta,
   o efeito, o objeto).

**End frame e encadeamento:**
- **Take N+1 começa exatamente onde o take N terminou** — o frame final do N vira o frame inicial do N+1, e o destino do N+1 já entra fixado. Sequência de 3 takes de 8s vira um plano de 24s com continuidade, não três clipes parecidos.
- **Take com destino obrigatório** (produto na mão, olhar pra câmera no fim, CTA card entrando) para de depender de sorte: fixa o frame final e o movimento se resolve pra chegar lá.
- ⚠️ **A ref manda no look** — end frame amarra COMPOSIÇÃO/posição, não substitui o controle estético da imagem base.

**Loop de rascunho 360p → take final.** Direção de movimento/enquadramento duvidosa: roda em **360p** (metade do preço), julga a MOÇÃO (não o frame), e só então gasta o take bom. Casa com a regra de nunca aprovar vídeo por print.

🔴 **Nunca pedir silêncio/mudo no prompt de vídeo Omni — derruba a geração inteira (`BLOCK_SILENCED_VIDEOS`).** O dispatch sobe com `mediaGenerationContext.audioFailurePreference:"BLOCK_SILENCED_VIDEOS"`: áudio MUDO conta como FALHA, não como "vídeo sem som" — o take inteiro é descartado. Termos que acionam: "silêncio absoluto", "no audio", "mudo", "sem som". "Sem música" continua seguro (nega só a trilha, não o áudio inteiro). Escrever **ambiente natural discreto** no lugar — `"quiet natural room ambience, no music, no voices"` — mesmo quando esse áudio vai ser descartado na montagem (ex.: template CapCut que já tem trilha própria). Medido 2026-08-25, ver `ref\changelog.md`.

Padrões do guia oficial (DeepMind + artigo do perfil oficial do Google Flow no X, 30/09/2026, *"Creative prompting with Gemini Omni in Google Flow"*). É o que a Google recomenda; o que o autor ainda não rodou está marcado **(não medido)**:
- **Edição:** nomear só a mudança e fechar com "Keep everything else the same".
- **Plano único:** segundo a Google, o Omni **corta entre ângulos por padrão**. Take que tem que ser um plano só (fala, demonstração) leva "single continuous shot, no scene cuts" escrito. Câmera: "oner", "locked off", "push in", "dolly zoom", "natural smartphone zoom".
- **Timecode dentro do take (não medido):** `[0-3s] ação A. [3-6s] ação B. [6-10s] ação C.` ou "At 2 seconds, …". Casa com o método de causa dentro de um take e com gesto sincronizado à fala.
- **Ritmo (não medido):** número ou rampa, nunca "rápido/lento": "4x the speed", "time ramps down into extreme slow motion", "at sec 3 … slows to 1% speed".
- **Transição entre ângulos (não medido; post do perfil oficial do Google Flow no X, 15/10/2026):** depois de achar a cena, o agente gera 4–6 imagens dela em outros ângulos (close, contra-plongée); no Omni, a 1ª frase é a ação e a **2ª só o estilo da transição** ("fade", "light blur", "glitch", "fast zoom").
- **Loop (não medido):** o mesmo frame no Início e no Fim + "create an endless loop using this start and end frame" (fundo de reels, card animado).
- **Look entre takes:** "Match the color grading of this image to my scene", com o take aprovado como ref.
- **Transição dirigida:** match-cut ("spinning coffee cup → Ferris wheel → analog watch face") ou transformação (luzes da estrada viram ondas).
- **Storyboard:** "follow the key beats exactly in order, top to bottom, left to right"; limitar em 10s.
- **Ingrediente:** dar nome ao asset quando sobe e citá-lo no prompt entre colchetes, com arroba antes do nome. Áudio de ref entra **dentro de um CLIPE DE VÍDEO** ("upload video clips for audio or motion reference"), não como arquivo de áudio.
- **Tipografia cinética (não medido):** "Word-by-word on screen: '…'", balão de fala, anotação à mão com seta. A régua de "Texto na cena" continua valendo: preço e dígito vão em overlay.
- **Negativas da lista oficial:** no dialogue · no scene cuts · no extra sound effects · no plastic textures · no camera movement. ⚠️ Nenhuma pede silêncio, mas "no dialogue" + "no music" + "no SFX" juntos podem virar vídeo mudo e cair no `BLOCK_SILENCED_VIDEOS`: deixe o ambiente escrito.

## Regras de prompt Omni (vigentes)

Uma regra por linha, sem data. Evidência e medições: `ref\changelog.md`.

- **Prompt de vídeo = prosa natural e detalhada** (ação, câmera, física, transição, timing), como explicaria a cena a um diretor humano. "Enxuto ganha" vale pra imagem estática, não pra moção. Julgue assistindo (Gemini multimodal com o mp4), nunca por frame.
- **Sintaxe mágica de criador** (`/comando`, "código secreto") não é feature do Flow: só entra em pipeline depois de A/B de controle (`/palavra` × `palavra` × `/nonsense`). O atalho documentado é o arroba: arroba + nome = personagem, arroba + "me" = avatar.
- **Texto na cena:** bloco `ON-SCREEN TEXT` dedicado, grafia entre aspas, um rótulo por vez, pedindo pra sumir antes do próximo. Texto grande e telefone saem certos; texto miúdo (tagline, rótulo) derrete. Confira cada dígito no zoom; o que precisa ser garantido vai em overlay.
- **Não peça pra apontar "algo escrito" e proibir texto na mesma peça** (o modelo obedece a proibição e aponta pro vazio). Rótulo sem texto: descreva positivo ("superfície lisa, sem inscrição"); "rótulo branco/vazio" virou a palavra "WHITE EMPTY".
- **Marca como ref desde a 1ª geração** (storyboard + ingredientes desde o começo rendem mais que remendo depois): sem a logo como ref o wordmark emborra. Logo GRANDE no ambiente sai legível; a pequena emborra.
- **Receita UGC ≠ receita de animação.** `locked off` + sem termo de cinema + sem trilha faz vídeo parecer caseiro; em personagem 3D entrega boneco imóvel. Pra dar vida, peça como AÇÃO (salto, squash and stretch, peso mudando de pé, luz varrendo, emissor pulsando), não como estilo.
- **Look de celular vaza pro áudio.** Separe os dois no bloco de áudio: voz em primeiro plano, de perto, clara, com bom volume, ambiente discreto sem música; e diga que a IMAGEM segue natural, mas que isso vale só pra imagem.
- **`VOICE` é bloco obrigatório em peça multi-take** (gênero, idade, registro, intenção, igual em todos os takes); sem ele cada take sorteia uma voz. Descrição natural de timbre funciona melhor que preset. Confira a voz entre takes no QC.
- **Fala de r2v tem que ser entendida de primeira:** frase curta, ordem direta, uma ideia por vez, a palavra que o público usa, preço isolado no fim. A fala conversa, a tela nomeia (nome técnico vai no overlay/card).
- **Escreva a fala COM acento** (o Omni pronuncia o que está escrito). Guia fonético só pra marca de pronúncia ambígua.
- **Fala que começa direto num dado** ganha palavra de abertura que você não escreveu ("Olá, veja bem!", "E"): preveja um corte de ~1s no editor em vez de regerar.
- **A duração do take casa com a fala:** 10s com ~5s de locução deixa silêncio morto. Encha mais ou use 4/6/8s.
- **Transcrição confere palavra, não fluência:** gagueira só a audição multimodal pega; take de fala vendida precisa de ouvido, não de diff de texto. Whisper erra negação sutil: valide o sentido da frase antes de ancorá-la no prompt.
- **Objeto aceso no frame-base:** peça o EFEITO (bloom, halo na parede, poça de luz) e a negativa ("must read instantly as a lit panel, never as a blank white square"), não o estado. Imagem custa 0: itere o frame-base antes de gastar vídeo.
- **Take de demonstração:** o personagem é o próprio produto demonstrando com o corpo; peça cada mudança "clara e completa, segurando até a próxima", uma por coisa nomeada. O QC é a sincronia entre o que ele DIZ e o que a imagem MOSTRA, assistindo.
- **Take barrado com prompt e ref limpos:** não cace palavra. Mude 1 variável por vez usando take JÁ aprovado como controle (fala do barrado sobre imagem aprovada; fala aprovada sobre imagem barrada). "Fala aprovada" = ESTA frase inteira, NESTE personagem; frase montada de pedaços não é controle.
- **Fix de produção do take barrado (n=1):** imagem já aprovada + só a fala muda (A-B-A é corte normal), com teto de tentativas declarado; barrou de novo, a peça fecha sem aquele take e a cartela carrega a informação.
- **Fechamento com escassez/contato na fala** barrou 2× no `<cliente-ótica-2>` ("vagas" + "garanta a sua") e 5× no `<cliente-H>` (03/11: neta na selfie com a vó dizendo "WhatsApp" e/ou "vagas limitadas"; senhor e rapaz com a mesma fala passaram): padrão observado, não gatilho provado. Escassez e WhatsApp vão pro selo/cartela; a fala leva outra ação ("Pronto, já garanti o dela. Faz igual, viu?" passou de primeira).
- **Ref de roupa/objeto com outro HUMANO = risco alto de merge de identidade:** use a peça isolada (foto de produto, flat-lay, ghost-mannequin), sem rosto, pele nem pose. Vale pra transferir atributo à pessoa do VÍDEO; quando a pessoa-alvo É a ref, o humano é o ponto.
- **Ref de fundo:** "BACKGROUND = THE ATTACHED PHOTO, UNCHANGED"; corte a descrição verbosa da cena (vira receita de redesenho) e não peça enquadramento mais aberto. O Omni não garante composite literal: pixel-exato = matte + composite local. Fundo com gente (n=1): guard explícito ("speaker é a única pessoa nítida em foreground; gente do fundo pequena, distante, sem trazer rosto pra frente").
- **Edição de take real re-dubla a fala por padrão.** Trava: bloco AUDIO dedicado ("keep the original audio exactly as recorded; do NOT re-voice/re-dub/paraphrase/shorten/extend, do NOT add any word"), transcrição EXATA entre aspas (citando as palavras que ele come), "if unclear, the recorded audio always wins", boca travada frame a frame, escopo mínimo ("your ONLY job is to replace the background"). Estrutura AUDIO / PERSON / BACKGROUND.
- **Fallback da trava de áudio:** voz saiu errada mas a boca seguiu → remonte o áudio original por cima (`ffmpeg -i omni.mp4 -i take.mp4 -map 0:v -map 1:a -c:v copy -c:a aac -shortest out.mp4`). Com áudio travado não peça SFX/música no mesmo prompt (a trava anula): SFX e música são do editor; Omni = VFX visual + voz preservada.
- **Take caseiro no edit:** "photoreal, not cinematic, no color grade, no background blur".
- **Card de CTA pronto animado via r2v** ("keep the ENTIRE card exactly, animate only …"): texto grande e telefones preservam, o miúdo deriva; confira no zoom. Card de design com texto PT = GPT Image 2, não Nano (o Nano traduz PT→EN).
- **Clean-plate** ("mantém a cena, remove pessoa + legenda + marca-d'água, reconstrói fundo coerente"): passe o aspect_ratio do frame (sem isso vira outpaint). Fundo não precisa de logo: logo quebrada → apague e refaça superfície lisa. Se alucinar cenário: "estender a MESMA parede, não adicionar nada", ou re-edite do frame original com prompt mínimo.
- **Prompt que cita pessoa/"foto de mulher"** dispara `UNSAFE_GENERATION` (likeness): reescreva falando de SUPERFÍCIES; se o plate-base viciou, re-edite do frame original.
- **Pessoa real composta em fundo:** bloco INTEGRATION ("UMA foto real, MESMA câmera naquela sala": relight, cor refletida, sombra de contato + AO, grão e exposição casados, leve DoF); sem ele sai colado. Fonte de baixa resolução: o Nano redesenha o rosto, o still valida o look e não a identidade.
- **Retrato de pessoa real:** descreva só o que a foto mostra e negue o que não tem ("sem óculos, clean-shaven"): traço hardcodado da pessoa errada vira traço inventado. QC: idoso continua idoso.

## Status de features

| Feature | Status | Playbook |
|---|---|---|
| Gemini Omni Flash 10s | ✅ padrão do autor | Omni |
| 🆕 Omni **1.1** — end frame (frame inicial + FINAL) | ✅ UI 27/08 | §PAINEL |
| 🆕 Omni Flash **1.0** no dropdown | 🔴 NÃO EXISTE MAIS (27/08) | §MAPA |
| 🆕 Aba **Frames** como modo próprio (end frame) — **mesmo preço** da aba Elementos com imagem | ✅ UI 27/08 | §PAINEL DE VÍDEO |
| 🆕 Tabela de créditos (4/6/8/10s · 360p/720p · x1–x4 · video edit) | ✅ MEDIDA 27/08 | §PAINEL DE VÍDEO |
| 🆕 Rascunho em **360p** = **metade** do 720p | ✅ UI 27/08 · MEDIDO | §PAINEL |
| 🆕 Export/upscale **1080p e 4K** (nativo = 720p) | ✅ UI 27/08 | §PAINEL |
| Omni edição nativa de vídeo multi-turn | ✅ padrão do autor | Omni |
| Omni com referências: vídeo/imagem/ingredientes/frames/avatar | ✅ mapeado | Omni |
| Input de áudio no Omni/Flow | ❌ TESTADO 08/06 — arquivo de áudio NÃO entra no Flow (áudio = só output). O guia oficial de 30/09 bate: áudio de ref vai **dentro de clipe de vídeo** | §Padrões do guia oficial |
| Storyboard→vídeo (grade 2x2 GPT → Omni) | ✅ VALIDADO 08/06 — segue ordem, mantém identidade/mood/produto; texto/logo emborra (overlay na montagem) | `ref\changelog.md` |
| Flow Agent para prompt funcional + validação nativa | ✅ útil com limites | Agent |
| 🆕 "Comando em barra" (`/studio`, `/transition`, `/orbit`, "códigos secretos") | ❌ NÃO EXISTE — folclore de criador; o Omni lê como palavra comum | `ref\changelog.md` 17/09 |
| 🆕 Arroba no prompt (arroba + nome = personagem, arroba + "me" = avatar, arroba sozinho = busca asset; o guia de 30/09 escreve entre colchetes) | ✅ documentado (Central de Ajuda + guia 30/09) · não testado pelo autor | `ref\changelog.md` 17/09 |
| Chrome MCP como automação principal | ❌ legado | §Fallback |
| Gerar imagem (Nano Banana Pro) | ✅ | P01 |
| Download do tile pelo ícone (1K) | ✅ | P01 |
| Validação multimodal (Read) | ✅ | P01 |
| Criar coleção (right-click área vazia) | ✅ | P02 |
| Menu de contexto da imagem (11 ações) | ✅ | P03 |
| Upscale 1K/2K/4K no download nativo | ✅ UI | P03 |
| i2v via "Animar" (imagem→vídeo) | ✅ | P04 |
| Lower Priority = 0 créditos | 🪦 **ACABOU no Flow (27/08)** — o mais barato hoje é 360p (metade) | §PAINEL |
| Batch x4 Nano Banana Pro (~60s) | ✅ | P05 |
| Edit view do vídeo (Estender/Inserir/Remover/Câmera) | ✅ descoberta | P06 |
| Durações 4s/6s/8s + Aspects | ✅ mapeado | MAPA |
| Rename coleção | ⏳ pendente | PENDENTES |
| Recortar+Colar mover | ⏳ pendente | PENDENTES |
| Elementos (ingredientes visuais) | ⏳ parcial | PENDENTES |
| Vozes personalizadas | ⏳ não achado | PENDENTES |
| Frame-to-video acesso direto (sem Animar) | ✅ RESOLVIDO 27/08 — **Frames** é aba de primeiro nível no modo Vídeo | §PAINEL DE VÍDEO |
| Avatar (clone rosto+voz de si mesmo) | ✅ onboarding mapeado | §Avatar |

---

## MAPA estável (stable descriptors — texto/ícone, não refs)

### Modelos

**Padrão de vídeo no Flow**: **Gemini Omni Flash 1.1** (multimodal, edição nativa multi-turn, end frame, 360p/720p). 🔴 **27/08/2026: o dropdown de vídeo tem SÓ 4 entradas — `Omni Flash 1.1` · `Veo 3.1 - Lite` · `Veo 3.1 - Fast` · `Veo 3.1 - Quality`. O Omni Flash 1.0 SUMIU e os tiers `[Lower Priority]` ACABARAM.** Custos e modalidades: **§PAINEL DE VÍDEO**.

**Imagem**: Nano Banana Pro (premium) · Nano Banana 2 · Imagen 4
**Vídeo legado/específico**: Veo 3.1 Quality (100 créd/vídeo na tabela oficial do Pro) · Fast (20 créd/vídeo) · Lite.
Todos Veo têm `volume_up` (áudio embutido).

### Aspect ratios

- Imagem: 16:9 / 4:3 / 1:1 / 3:4 / 9:16
- Vídeo: 9:16 / 16:9

### Durações de vídeo

**4s / 6s / 8s / 10s** (10s = teto por geração) + variantes **x1–x4**. No **video edit** a duração é travada pelo vídeo-fonte. Preço e pegadinhas: **§PAINEL DE VÍDEO**.

### Resolução

Geração: **360p** (rascunho, metade do preço) ou **720p** (nativo). **1080p/4K = export**, não geração. Detalhe: **§PAINEL DE VÍDEO**.

### Custo em tempo real

UI mostra `"A geração vai usar X créditos"` acima do botão Criar. Sempre checar antes de disparar.

### Paralelismo simultâneo

- Imagens (pago): 12-16
- Vídeos (pago): 6-10

### UI — elementos com stable descriptors

| Elemento | Identificar por |
|---|---|
| Prompt | `<div contenteditable="true">` placeholder "O que você quer criar?" (NÃO é input/textarea) |
| Seletor modo/modelo | button com texto `🍌 Nano Banana Pro x1` ou `Veo 3.1 - X xN` (canto inferior direito) |
| Botão gerar | button com `arrow_forward` + "Criar" (⚠️ NÃO o `add_2 Criar`) |
| Menu contexto imagem | right-click em imagem gerada |
| Menu contexto área vazia | right-click em espaço livre do grid (→ Criar coleção, Colar) |

---

## Receitas (P01–P06)

### P01 — Gerar 1 imagem + baixar + validar ✅

Seguir a ROTA VIVA (passos 1, 3, 4, 6, 8): projeto em flow.google.com/project/<id> → Imagem + x1 → .focus() + computer type → clique real em Criar → baixar pelo ícone (1K). Validar com Read.

### P02 — Criar coleção ✅

Right-click em área vazia do canvas → menu com `folder Criar coleção` + `content_paste Colar` → click → coleção "Coleção sem título" aparece no grid.

### P03 — Menu de contexto da imagem (hub de features) ✅

Right-click em imagem gerada revela:

| Ação | Uso |
|---|---|
| `motion_blur Animar` | i2v 1-click (→ P04) |
| `add Incluir no comando` | Ingrediente 1-click |
| `download Baixar` | → submenu **1K / 2K / 4K** (upscale integrado) |
| `undo Reutilizar comando` | Regen mesmo prompt |
| `whiteboard Renomear` | Rename inline |
| `content_cut Recortar` + Colar | Mover pra outra área/coleção |
| `content_copy Copiar` | Copiar |
| `archive Archive` | Arquivar |
| `favorite` / `share` / `flag` | Favoritar/Compartilhar/Reportar |

### P04 — i2v via Animar ✅

```
1. (opcional) Selecionar modelo Veo antes.
2. Right-click imagem → "motion_blur Animar"
3. UI muda automaticamente pra Frames-to-video:
   - Imagem vira 1º frame
   - Botão "Trocar primeiro/último" e "Final" aparecem
   - Prompt limpo pra descrição da animação
4. Escrever prompt animação + click arrow_forward Criar
5. Detectar: `document.querySelectorAll('video').src` tem media.getMediaUrlRedirect
6. Quando pronto: videoWidth > 0 + duration > 0
7. Download: mesmo fluxo P01
8. Validar: ffmpeg extract frame (`ffmpeg -i in.mp4 -ss 00:00:04 -vframes 1 out.png`) → Read
```

### P05 — Batch x4 Nano Banana Pro ✅

```
1. Abrir menu modelo → tab Imagem + x4 + Escape
2. Escrever 1 prompt no contenteditable
3. arrow_forward Criar (único click)
4. ~60s depois: 4 IDs únicos no DOM
5. Baixar cada tile pelo ícone
```

**Uso**: explorar variações de um conceito único. Mesmo custo que 4 gerações separadas, mas 4× mais rápido.

### P06 — Edit view do vídeo ✅ descoberta (operação a mapear)

Click em vídeo no grid → `/project/<id>/edit/<media-id>` abre player + 4 ações:

- **Estender** (só Lite/Fast; Quality não tem) — continua o vídeo, encadeia segundos
- **Inserir** — adicionar objeto/cena
- **Remover** — inpaint (apagar objeto)
- **Câmera** — controle de movimento

Útil pro canal: encadear 3-4 Estender Lite → shorts de 30s+.

---

## Avatar — clone de si mesmo (rosto + voz) — descoberto 2026-05-21

Aba **Avatar** na sidebar esquerda (Tudo / Imagens / Vídeos / Vozes / Personagens / **Avatar** / Uploads). Cria um avatar IA do próprio usuário gravando rosto + voz, pra gerar conteúdo com a própria imagem em serviços Google.

### ⚠️ A CAPTURA DO AVATAR É SÓ EM INGLÊS

A 1ª tela do painel Avatar traz, literal: *"Disclaimer: the avatar capture service is in English only."* Os números/palavras pedidos durante a gravação têm que ser ditos **em inglês** — falar em português reprova. Não há versão PT; idioma do telefone/conta não muda isso.

### Fluxo de telas antes da câmera

1. Painel Avatar "Seu rosto, sua voz, sua história" → botão **Comece já**
2. Consentimento (em `account.google.com`): "Create an avatar that looks and sounds like you" → **I agree** / No thanks
3. "Tips for your selfie and voice recording" → **Next**
4. Câmera: mover a cabeça em direções + dizer palavras/números **em inglês**

### Requisitos pra passar (tela de tips)

- Dono da conta, 18+ (sem menores gravando)
- Celular na altura dos olhos; luz média (nem escuro nem estourado)
- Olhos/nariz/boca visíveis — óculos de grau OK; sem óculos escuros/máscara/boné
- Ambiente silencioso, sem outras vozes ao fundo
- **Sem pessoas nem imagens de rostos no fundo** — pôster de anime, figure, TV ligada reprovam
- Dígitos ditos em inglês, um por vez, pausado

### Pendente

- Interação exata da tela de câmera (qtde de dígitos, agrupado vs dígito-a-dígito, sequência de movimentos) não mapeada — o autor estava testando em 2026-05-21.

---

## Gotchas críticos

- **G01**: dois botões "Criar" — `add_2 Criar` (adicionar item, inútil) vs `arrow_forward Criar` (gerar, este). SEMPRE filtrar por `arrow_forward`.
- **G02**: prompt é `contenteditable`, não input. Ler via `.innerText`, não `.value`.
- **G04**: default é **Vídeo x4 Quality** = 400 créditos/disparo. Trocar antes de testar.
- **G05**: múltiplas extensões Claude conectadas → `tabs_context_mcp` falha. Pedir user clicar "Connect" no browser certo.
- **G07**: estado i2v (Frames ativo) prende o seletor no "Vídeo x1" sem nome de modelo. Resetar: navigate + F5.
- **G09 (descoberto + resolvido 2026-04-22 sessão 2):** prompt digitado **pode não chegar ao React state** após sessão longa ou muitas tentativas de set via JS. Sintomas: campo visualmente tem o texto, mas Flow responde "Você precisa fornecer um comando" ao clicar Criar. **SOLUÇÃO CONFIRMADA: F5 / page refresh** — limpa o state zumbi do React e permite `computer.type` funcionar normalmente. **Prevenção:** ao trocar entre projetos ou depois de tentar setar prompt via JS (innerText/execCommand) e falhar 1×, dar F5 antes de tentar de novo. Não insistir em múltiplas tentativas sem refresh — só gera mais state zumbi.
- **G10 (confirmado 2026-04-22 sessão 3):** duplo clique ou dois `criar.click()` em sequência geram 8 imagens em vez de 4. **SOLUÇÃO:** 1 clique só via `criar.click()` + aguardar geração antes de qualquer outra ação. Se precisar confirmar o click registrou, ler state da UI (aparece contador ou placeholder de progresso) — não re-clicar.
- **G11 (confirmado 2026-04-22 sessão 3):** prompt fragmentado em `TOPO: x / MEIO: y / BAIXO: z` ou em várias frases curtas faz o modelo gerar múltiplas imagens separadas (uma por seção). **SOLUÇÃO:** prosa corrida declarativa em 1 parágrafo descrevendo o card como infográfico integrado. Evitar listas e bullets no prompt.
- **G12 (observado 2026-04-22 sessão 3):** prompt com termos farmacêuticos (nome de produto composto, claims médicos) ocasionalmente bloqueado pelo filtro de moderação ("violar nossas políticas"). Taxa observada ~25%. **MITIGAÇÃO:** usar padrão "1/4 fuga" — gerar x4 e descartar o bloqueado; se 2+ bloquearem, rephrasear para remover termo problemático e focar em benefício genérico.
- **G13 (observado 2026-04-22 sessão 3):** quando o campo prompt está vazio e o `computer.type` começa antes do React terminar de processar o focus, os primeiros ~50-60 chars do texto caem silenciosamente. **SOLUÇÃO:** após `left_click` no contenteditable, `wait 1s` + digitar 2-3 espaços de buffer antes do conteúdo real. Sempre validar via `ed.innerText.slice(0, 200)` antes de clicar Criar.
- **G14 (2026-04-22 sessão 3 — aprendizado do user):** **não inflar prompt com controles que a UI já tem.** "1:1 / quadrado / aspect ratio" configura no seletor do modelo, não no texto. "UMA UNICA IMAGEM" só fazia sentido como contramedida ao G11 — com prompt em prosa corrida não precisa mais. Regra: **prompt é conceito + hierarquia + mood. UI é formato + modelo + quantidade.** Cada palavra de controle no prompt rouba atenção do conceito.
- **G15 (2026-04-22 sessão 3 — aprendizado do user):** **para peças de cliente, ingredientes ANTES da geração, não depois.** Se o brand-profile pede logo + produto, upload via `mcp__Claude_in_Chrome__file_upload` + `+` do prompt bar (ou menu contexto de imagem já no grid → "Incluir no comando") antes de digitar texto. Gerar card "esqueleto" com espaço reservado pra depois fazer i2i é perda de ciclo — duas rodadas de moderação, dois custos de atenção, resultado inferior ao single-pass com ingredientes.

---

## Quota (preferência do autor; os preços citados são medidos)

- O saldo é o número visível na conta; o aviso "A geração vai usar N créditos" é o árbitro de cada disparo.
- Pequeno/médio: pode validar, mas checar sempre o aviso `"A geração vai usar X créditos"` antes de criar.
- Lote grande (dezenas+) ou qualquer sequência Omni com muitos takes: **pergunta antes**.
- Rascunhe em 360p (metade do preço, não zero) e gaste 720p só no take aprovado.
- 🔴 **O que pesa é o TIPO DE INPUT e o número de takes, não a aba.** Imagem (frame ou elemento) = tabela base; **vídeo de entrada (video edit) = +67%**. E **fatiar custa**: 2×4s (14) > 1×8s (12). Fórmula em §PAINEL DE VÍDEO.

Não deixar a quota dominar a análise: ela importa, mas a decisão de 22/05/2026 é de capacidade. Omni virou padrão por qualidade, edição e multimodalidade, não só por preço.

## Output padrão (preferência do autor)

```
<pasta-de-midia>/tmp-flow-poc/                 ← testes descartáveis
<pasta-de-midia>/flow-out/<projeto>/            ← por cliente/projeto
  imagens/
  videos/
  ingredientes/
```

---

## Manutenção

Descoberta nova reescreve a linha que já fala do assunto; diário de sessão vai em `ref\changelog.md` (o autor também guardava uma nota de sessão no vault dele, não incluída).

## Pendentes ativos (explorar próxima sessão)

- **Rename de coleção**: right-click no card só mostra "Excluir"; F2/double-click não ativam edit. Hipótese não testada: entrar DENTRO da coleção (click no card abre?) e renomear lá.
- **Recortar+Colar pra mover**: menu da imagem tem "Recortar", menu da área vazia tem "Colar" — mas mover pra dentro de coleção precisa de navegação ainda não validada.
- **Elementos** (ingredientes visuais): tab ativa mas UI visível não muda. Hipótese: precisa arrastar imagens do grid como referências.
- **Vozes personalizadas**: não localizado na UI. Memória nano-veo menciona "modo ingredientes habilita voz" — provável só aparece quando Elementos tem conteúdo.
- **Frame-to-video acesso direto** (sem Animar): tab `crop_free Frames` no menu existe mas acesso sem imagem fonte não testado.
- **Edit view operações**: Estender/Inserir/Remover/Câmera descobertas mas fluxo operacional não testado.
