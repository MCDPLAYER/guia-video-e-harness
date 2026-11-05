# Laboratório da motion-studio: ⏳ EM TESTE

O `SKILL.md` é o que já foi **validado em peça real**. Aqui ficam as ideias de fora (artigo, kit, viral, palpite meu)
que **ainda não provaram nada no nosso trabalho**. Nada daqui entra numa peça sozinho.
Aberto em 30/09/2026 com um kit de terceiro ("Motion Video Kit", licença MIT, em inglês). A cópia do kit **não** vem neste
repo (a MIT exige manter crédito e texto da licença): procure o original pelo nome. Os arquivos citados abaixo são do kit.
Backup da skill antes do laboratório: um zip da pasta da skill (fora do repo).

> **Teto: ~100 linhas.** Entra um, sai um. Laboratório que só cresce vira sótão.

## O núcleo que nenhum teste pode tirar
Foi isto que fez os vídeos subirem de nível (POC-01, reels e VSL do `<cliente-app>`). Experimento que exija tirar qualquer um está fora:
1. O quadro é função pura do tempo (`seek(t)`), e o Opus escreve o código e olha o resultado.
2. Uma forma que se transforma: todo estado novo tem um portador vindo do anterior.
3. As tiras de 30 quadros de cada transição (gate 2). Os defeitos de acabamento só aparecem ali.
4. Um revisor Opus novo, às cegas, contra um viral do mesmo gênero (gate 4). E a régua final é o usuário.

## Como um teste roda
- 🟢 **Só mede:** lê o vídeo pronto e não muda nada. Pode rodar em qualquer peça. O resultado vai no relatório como
  informação, **nunca como nota de corte**.
- 🟡 **Muda o processo, não o vídeo:** roda numa peça real **somando** ao processo de hoje, sem trocar nenhum passo.
- 🔴 **Muda o vídeo:** só como **versão B**. A = método de hoje, intocado. B = A + **UM** experimento (um só, pra saber
  o que causou a diferença). Julgam o revisor às cegas (sem saber qual das duas é a nova) e o usuário. Enquanto não
  provar, é só em peça curta (Reels até 45 s): numa VSL de 4 min, a versão B custa +48 min de render.
- **Promover:** ganhou numa peça real e o usuário aprovou. Aí vira UMA linha no `SKILL.md`, reescrevendo uma que já
  existe (teto de 150), e o item sai daqui com a data.
- **Descartar:** perdeu, ou o usuário não gostou. Vai pra *Descartados* com uma linha de porquê, pra ninguém reabrir.
- **Validade:** 30 dias sem linha no *Registro* = a revisão semanal do caderno (sexta, rotina do autor) propõe o descarte ao usuário.
- **Usou algo daqui numa peça:** uma linha no *Registro* (data · peça · item · como foi julgado · resultado) e o aviso
  **"usei E3, em teste"** no resumo pro usuário. Sem linha no Registro, o teste não existiu.

## Na bancada (E1 a E9 entraram em 30/09/2026; item novo leva a data)
**▶ = vai na PRÓXIMA peça curta (o autor, 20/10): E2, E3, E4 e E6.** Cada uso vira linha no Registro.

**E1 🟢 Medidor de tela parada** · `em-teste/tela-parada.sh video.mp4` · aponta o segundo exato de cada pausa.
Calibração (30/09): virais de UI da nossa pasta de refs = 7,8 a 12,1 s parados a cada 30 s (pausa de até 3,7 s);
POC-01 v3 = 8,8; reels do `<cliente-app>` aprovado = 3,3 (máx. 0,7 s). **A v2 (perdeu) e a v3 (ganhou) medem igual, então o número
não separa bom de ruim.** Uso: lista de pausas pro olho conferir na tira. Promove se, em 2 peças, apontar uma
pausa que os gates 1 a 3 não pegaram e o revisor confirmar.

**▶ E2 🟢 Triagem de som antes de ouvir** · `scripts/sfx-candidates.py` do kit · reprova o que ronca (>50% da
energia abaixo de 150 Hz), o que chia (>40% acima de 6 kHz) e o que é longo (>0,8 s). Primeiro teste: rodar nos sons
que o `sfx.py` gera hoje e ver o que ele diz.

**▶ E3 🟡 Rodada de verificação + livro-caixa** · fonte: `references/gauntlet.md` do kit e o *Verification critic*
de `critic-prompts.md`. O revisor N+1 recebe a lista de P0/P1 do N (**nunca** o que eu acho que consertei), marca cada
item como RESOLVIDO, PARCIAL ou AINDA LÁ, caça regressão e fecha com ENTREGA ou MAIS UMA PASSADA (no máximo 3
correções). O livro-caixa registra rodada → achados → mudança → número antes/depois. Hoje o `REVIEW-PROMPT.md` não
tem essa parte, e a parada é no feeling. Aplicar como **anexo** ao prompt do gate 4, sem reescrever o prompt. Promove
se der um critério de parada mais claro sem pegar menos P1.

**▶ E4 🟡 Revisor no storyboard** · fonte: *Storyboard critic* de `critic-prompts.md`. Antes do código, um revisor novo
lê só o brief e o storyboard: a ordem bate com o serviço real? toda afirmação está provada? cada beat tem composição e
função próprias? qual beat é enchimento? o CTA fica claro no mudo? Custo: minutos. Promove se pegar pelo menos 1
problema que teria virado P1 depois do render.

**E5 🟡 Peça isolada antes do filme** · só pra coisa longa (VSL), não entra em Reels curto (o autor, 20/10). Cada estação é renderizada sozinha (quadros + 3 a
4 s) e revisada antes de entrar. Fonte: `gauntlet.md` e `templates/component-lab.html`. Promove se a VSL seguinte
fechar com menos renders inteiros.

**▶ E6 🔴 Quadro 1 pronto** · o 1º quadro é uma composição acabada, porque o feed mostra ele parado. Achado (30/09):
o reels do `<cliente-app>` abre com a tela azul quase vazia e só a fenda da impressora, e a VSL abre com a máquina meio transparente.
Teste: versão B com o 1º quadro já montado, e o revisor compara as duas capas. Promove se o revisor e o usuário preferirem.

**E10 🔴 Rascunho no Sonnet, refino no Opus** (19/10) · o Sonnet 5.5 (subagente, `model: sonnet`) escreve a base e
o Opus só melhora pelos gates 1 a 3. Fere o núcleo nº 1 na escrita, por isso é só versão B, e a decisão é do usuário. Bancada 19/10: base Sonnet com render limpo e
estrutura que aguentou o refino sem reescrever, cota bem menor (nota `BANCADA-MOTION-MODELOS.md`, não incluída). Teto = a ideia da base; pedir fio narrativo. Promove se, numa peça real, B empatar com A no revisor às cegas e no usuário.

## Consulta livre (repertório, não regra)
- **16 tipos de transição + notas de 28 filmes de lançamento, com link** (`motion-grammar.md`, `launch-film-notes.md`):
  cardápio pro storyboard, e candidatos a referência B do revisor quando o gênero for lançamento de produto.
- **3D e produto físico** (`three-js-patterns.md`, `product-hero-realism.md`), pra quando aparecer (haste de óculos
  abrindo, embalagem):
  - câmera entre 35° e 55°, visual de maquete;
  - medir o pixel da cor da marca depois do render (a luz desvia a cor);
  - camada que sobe com smootherstep (ease-out dá um pulo de 1 quadro);
  - rótulo em HTML projetado do 3D;
  - movimento de produto real medido quadro a quadro no vídeo real;
  - motion blur: 10 subquadros deixaram "pente" em borda rápida no kit e nós usamos 4, então conferir na tira do
    movimento mais rápido.

## Descartados
- 30/09 · Régua do kit "≤1 s parado a cada 30 s": é de filme de lançamento de SaaS, e os nossos virais de referência
  param de 8 a 12× isso (ver E1). O medidor fica, a régua não.
- 30/09 · Agente de agência no Telegram (Hermes) e vídeo pela Replicate/Seedance: já temos a orquestração e o Flow.
- 15/10 · **snapcn** (componentes Remotion, MIT): 4 de 8 batem nos Banidos (troca por fade com quadro vazio, brilho, cinza no meio do fade, celular a ~15% parado metade do tempo); marca-texto erra a largura da palavra. Nada que o motor não faça. **motion-prompt** (120 movimentos, MIT): ficha boa, mas o vocabulário é de manual; achou 1 dos 6 beats de uma peça do `<cliente-F>` (#005, que já usamos). Se servir, é como vocabulário de câmera/luz em prompt do Flow, não aqui.
- 20/10 · **E7 som de biblioteca** e **E8 música de biblioteca**: o som de hoje passou na aprovação (`<cliente-app>`) e a música é Lyria, barata e personalizada (sem licença a explicar). Só volta se um cliente reclamar de "som amador". **E9 duas aberturas × dois formatos**: é ideia de entrega/formato pro cliente, não técnica de skill; fora do laboratório.

## Promovidos
(nenhum ainda)

## Registro de testes (a revisão semanal lê daqui)
<!-- data · peça · item · como foi julgado (revisor às cegas / usuário / medição) · resultado: ganhou / perdeu / inconclusivo -->
- 19/10 · bancada (prompt padrão, 25 s) · E10 · Argon→Opus, Sonnet→Opus e Opus sozinho lidos por mim, sem revisor · autor: "o sonnet se mostrou competente pra rascunhar" · inconclusivo (prompt aberto, cada modelo foi pra um lado; falta peça real)
- 15/10 · explicador do canal (4 quadros, `<pasta-de-saida-do-flow>\vox-teste\`) · colagem Vox na rota 3 (âncora no Nano 2.1 → 3 quadros com ela de ref, 0 créd) · lido por mim · quadros bons e coerentes, PT do título certo · animado no Omni (Frames, quadro = Início, 4 takes 720p = 51 créd) + narração Gemini TTS `pt-br-storyteller-11` + Lyria A/B → `<pasta-de-renders>\<explicador-01>\` (34 s). Moção ficou chapada como papel em 3 de 4; o take 1 apagou a imagem da TV aos 3,3 s (usei só o trecho bom, a 0,5×) · autor: lindo e calmo, as 2 músicas boas, mas LENTO e MONÓTONO · ⏳ refeito no motion-studio com os SPRITES recortados dos 4 quadros (rembg + inpaint, câmera viva, evento a cada ≤0,6 s, vermelho → lacre → carimbo → fatia) + versão pixelada (image-to-pixel, paleta fixa 16, bayer4, 135 px) · autor: pixel por FILTRO em cima do papel = mistura errada (cada estética compõe diferente: pixel pede roteiro e composição próprios); papel "ok", mas o movimento era de UI (curva lisa A→B) e papel é trêmulo, imperfeito · ⏳ v3 `?papel`: stop-motion de recorte (pose 2 quadros a 12/s, tremor por peça a cada pose ±1,6 px/±0,6°, tempo da pose ±30 ms, sem blur/fade, borda cortada irregular, 24 fps sem tmix) · autor: "bem melhor", mas o tremor excessivo dá dor de cabeça · refs da pasta "Inspiração de edição" (colagem/recorte, 10–12,5 q/s) medidas por `phaseCorrelate`: câmera parada (0,01–0,03% da largura por quadro) e peça quieta não treme; a v3 tremia tudo, câmera junto (0,4–2%, pico 9%) · ⏳ v4: tremor só na peça que se mexeu na pose (mede a pose anterior), câmera lisa a 24 q sem tremor, borda do vermelho só recorta quando muda de tamanho · autor: "ok, falta refino, mas pra um teste tá ótimo" (15/10)
- 15/10 · peça da `<parceria-musical>` em PIXEL NATIVO (`<pasta-de-renders>\<pixel-01>\`, 23,1 s 9:16) · pixel desenhado como pixel, não filtro: mundo 180×320 ×6 neighbor, paleta 16, fonte bitmap 5×7, sprite por pose, trocas por dither bayer/rolagem/clarão de 2 q, tremor inteiro, 30 fps sem blur; capas nacionais e emotes reduzidos a ícone por paleta curta (`assets.py`); narração com a voz clonada do autor (Gemini TTS, paga) + Lyria B cortada na pausa com o drop no CONTINUE + SFX chiptune do EVENTS · gates 1–3 limpos, mix −14,1 LUFS / pico −1,1 · revisor às cegas contra MV pixel (Slow GM, Stardew) · aguarda a régua
