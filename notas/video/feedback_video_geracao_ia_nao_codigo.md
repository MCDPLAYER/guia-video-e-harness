---
name: feedback_video_geracao_ia_nao_codigo
description: "quando recall: posso entregar video em Remotion/HyperFrames? o autor aceita motion-graphics por codigo? Qual default pra ad/reel/clipe, Omni/Flow/Veo? Geracao IA travou na moderacao, caio no codigo? Remotion serve pra entrega final hoje?"
metadata:
  node_type: memory
  type: feedback
  modified: 2026-09-28T18:00:25.128Z
---
> 🎬 **28/09/2026: o "método" desta nota foi substituído pela skill `motion-studio`** (o Opus escreve e olha, revisor Opus às cegas contra viral, sem Gemini; `video-remotion` arquivada). Vídeo gerado por IA segue Omni/Flow. **Validado em cliente no mesmo dia:** o reel motion do <cliente-app> (42,5 s, locução + trilha ElevenLabs + legenda) foi aprovado pelo autor; logo, motion por código é formato de entrega a cliente, não só POC. O resto da nota é histórico.

🔄 **REVISTO EM 2026-09-11 — a rejeicao de 12/06 NAO vale mais como veto.**

**Hoje:** Remotion esta **APROVADO COM METODO**. O que mudou nao foi o Remotion — foi
existir um **critico que enxerga o video** (Gemini 3.8 assiste o MP4 com som) e um loop
que mede. Sem esse loop, o resultado volta a ser o "deprimente" de junho.

Ferramenta e metodo moravam na skill `video-remotion` (arquivada em 28/09; hoje e a `motion-studio`, ver topo).

**Historico (por que a nota existia):** em 2026-06-12 o autor chamou o output de
HyperFrames/Remotion de "deprimente / horrivel". A condicao de reavaliacao escrita na
epoca era *"ate surgir uma ferramenta boa de USO sobre elas, que ele consiga dirigir sem
programar"*. Ela surgiu: o **Studio do Remotion** ficou interativo (arrasta no canvas,
keyframe de CSS e props, editor de easing) e **escreve de volta no codigo**.

**Onde o codigo GANHA (usar sem hesitar):**
- o que se **repete** e o que e **parametrico** (lote, N variantes do mesmo template)
- o que precisa de **precisao de frame**: sincronia com fala, dado, escala
- **editar video ja gravado**: legenda queimada, corte por alignment, overlay, reframe
- **3D real** sobre footage (material fisico, luz de estudio, sombra projetada)

**Onde o codigo PERDE (nao insistir):**
- **gerar** video por IA continua sendo Omni/Flow/Veo. Remotion nao substitui.
- peca hero unica e artesanal: a mao do editor humano (o autor) e melhor e mais rapida.

**How to apply:**
- Pedido de motion/overlay/legenda/3D/lote por codigo → abre a skill `motion-studio` (antes era a `video-remotion`).
- Pedido de gerar imagem/video por IA → continua `gflow`/Omni/Veo.
- ⚠️ **Nao rode o loop sem os guards** do `criticar.sh` — o passo que verifica tambem
  falha, e falha CALADO (4 formas distintas numa so sessao: `status: SUCCESS` com corpo
  vazio, colisao de instancias, processo orfao segurando o log, e master pesado demais
  estourando o timeout). Critico que falha calado da sensacao de medicao sem medir.
- 🔴 **REVISOR DE CODIGO E INUTIL AQUI; a lente tecnica vale como INSTRUMENTO (15/09).**
  O autor: *"o final e um video e nao um sistema; minha timeline de edicao e uma macarronada horrenda e o
  video sai impecavel"*. Ele esta certo: a peca e descartavel, o codigo morre com o render. Clean
  architecture, seguranca, escalabilidade, "reutilizavel", cobertura de teste = **desperdicio puro**.
  **Mas** o agente que le a fonte e o unico com acesso a coordenada, duracao e tamanho reais — e foi o
  unico que pegou os 3 defeitos visuais que o autor viu na v15 (*"esse texto fica 0,87s, o piso e 1,2s"*,
  *"esse brilho esta 190px fora do balao"*, *"essa fonte tem 21px, o piso e 26"*). ⇒ **so vale achado
  que termine num NUMERO DA TELA.** Proibir arquitetura/seguranca/escala **por prompt**.
  📐 **O teste:** em artefato descartavel, revise o RESULTADO, nunca o meio de producao — e o meio so
  entra na conta quando ele MEDE o resultado.
- 🧠 **O default do modelo e o inimigo em codigo-pra-video, e o autor diagnosticou antes de mim:**
  *"quando uma LLM pega a ideia de codar, o vies padrao do treinamento e se preocupar com coisas
  convencionais como seguranca, clean architecture, escalacao"*. **Esta certo, e a causa e distribuicao
  de treino:** quase todo codigo do mundo e software que roda continuamente, com usuarios e ameacas;
  codigo de video e `f(frame) → imagem` que roda uma vez e morre. Onde isso ja custou versoes:
  componentizar em `<Sequence>` (higiene de software) quebra `useCurrentFrame()`, que passa a devolver
  frame LOCAL; particula com estado acumulado quebra porque o Remotion renderiza **fora de ordem**;
  `tsc` limpo e tipagem, **nao e a peca funcionando na tela** (o teste certo e uma composicao que
  RENDERIZA cada componente). **A peca nao precisa estar certa como sistema — precisa estar certa
  como imagem no frame N.**
- 🚨 **O maior risco nao e tecnico, e de projeto:** o pipeline otimiza o mensuravel
  (sincronia vira numero) e ignora o que nao vira numero (se ressoa, se da leitura,
  se o formato serve). Ver `PONTOS-CEGOS.md` no projeto (nao incluido) antes de comecar peca nova.
