# Lib `shaders`: material, luz e transição de shader no motor `seek(t)`

**Em teste desde 06/10/2026.** Fez um showcase (`showcase-*`), e o autor reagiu com "UUUUUUUUUUUU lindo". Ainda não
foi usada em peça de cliente.
- **O que é:** pacote npm `shaders` v4.0.0, licença MIT (conferida no `package.json` do pacote). Roda em WebGPU no
  Chrome.
- **Entra no menu, não vira padrão.** Agrega ao flat o que ele não faz: material de verdade, como vidro refratando o
  fundo, cromo com reflexo correndo, cristal abrindo arco-íris e metal líquido. Não substitui o flat em UI, dado ou
  fluxo.
- **Projeto completo:** `<pasta-de-renders>\shaders-showcase\` (não incluído). Final em `final\shaders-showcase.mp4`.

## Kit nesta pasta
- `vendor/shaders.iife.js`: bundle já com o PATCH (pronto pra usar). Pra refazer:
  1. `npm i shaders esbuild`;
  2. `esbuild vendor/entry.js --bundle --format=iife --outfile=vendor/shaders.iife.js`;
  3. no objeto que o `createShader` devolve (procure `getFailureReason`), acrescente `_r: () => i && i.renderer`.
- `registry.json`: todos os componentes com as props e os defaults (`node dump.mjs` regenera). **Leia aqui antes de
  chutar prop.**
- `mksdf.py nome "texto" [peso] [ocupação]`: gera o SDF do texto 3D (Sora, 512², Uint16) e `sdf/nome.js` com
  `SDF`/`SDFM`. SDFM traz a largura e a altura da caixa do texto, usadas pra casar a largura de duas linhas.
- `showcase-index.html`: o motor inteiro (cenas, transições, câmera, kicker, eventos de som). O resto do pipeline
  (render, sfx, bed, finish) é o padrão da skill.

## Como pluga no `seek(t)` (determinístico: md5 igual vindo de qualquer caminho, inclusive seek pra trás)
- **Um canvas por cena**, empilhado com z-index decrescente. Cada cena é
  `createShader(cv, {components}, {disableTelemetry:true, observeElement:false})`, seguido de `pause()` e
  `R = inst._r()`.
- **Quadro:** para cada cena ativa, primeiro `inst.update(id, props)` com tudo que for função de t, depois
  `await R.renderSyntheticFrame(t - clockDaCena)`. Antes de capturar, esperar 2 rAF.
- **Transição = componente que EMBRULHA a cena que sai** (`children`). Ela anima `progress` de 0 a 1, e o que dissolve
  fica transparente, mostrando a cena de baixo. A cena de baixo já está ativa desde o corte.
- **Texto 3D:** os componentes de forma (LiquidMetal, Glass, Crystal, Obsidian, Nebula, Holographic, Plastic, Water,
  Chrome, LightEdge) recebem:
  - `shapeType:'svgExtrude3D'`;
  - `shape` = JSON `{type, depth .12, bevel .02, rotX, rotY}`;
  - `shapeSdfUrl` (blob do SDF), `center` (y=0 é o topo) e `scale`.

  Pra girar, troque `rotX`/`rotY` no `shape` a cada quadro.

## Materiais que funcionaram (props fora do default)
- **Glass** sobre Stripes (filho): thickness .55, refraction 1.5, aberration .75, innerZoom 1.8, highlight .9,
  fresnel .5 e fresnelSoftness .25. Com o default a palavra some nas listras.
- **Chrome** no escuro: environment 1.7, softness .15, curvature .7. O brilho que varre é `envRotation` em mola.
  Com o default sai fosco.
- **Sparkle** embrulhando o cromo: a estrela acende com `intensity` (4→20) e `rayLength` (5→14) num kick.
- **Prism** + Crystal: o feixe anda pelo `position`. Godrays: `intensity` em kick no drop.
- **Plastic e Water:** precisam de `StudioBackground` tingido (piso e luz). Em cor chapada parecem outra peça.

## Transições medidas na tira (06/10)
| Transição | Veredito | Ajuste |
|---|---|---|
| IrisWipe, LinearWipe, BarnDoors, BlockDissolve, PagePeel (`amount` ×1,25) | ✅ limpas | — |
| NoiseDissolve | ✅ com `scale` ≥ 5 | com scale 3 sobram manchas lentas |
| RippleWipe | ⚠️ `rings` quantiza o raio: 3 anéis = anda aos degraus (3 quadros parado) | 14 anéis, feather .03; feather .1 = anel escuro sujo |
| SliceWipe | ❌ com texto | DESLOCA as fatias: arrasta pedaço duplicado da palavra velha. Usar VenetianBlinds |
| VenetianBlinds, RadialWipe | ✅ | ver o rabo, abaixo |

**O rabo do ease-out deixa lasca de 5 a 6 quadros no fim de qualquer máscara.** Correção: progresso × 1,12 a 1,3,
com teto em 1.

## Pegadinhas
- Giro de entrada (rotX) acima de ~52° mostra a extrusão de lado, um borrão ilegível. 52° com mola rápida resolve.
- Título 3D em 9:16: margem lateral de 12% ou mais. A câmera empurra no fim e a palavra entra na coluna de ícones.
- 10 materiais numa peça = mostruário. Revisor às cegas, rodada 1: perdeu do viral em continuidade (5×9) e coesão
  (5×9). **Em peça real: 1 ou 2 materiais**, e a lib como abertura, título ou fecho.
- Custo: 15 s em 9:16 com 10 cenas = render leve completo ~4,5 min (⏳ medido 10/2026). Cada cena ativa é um
  renderizador WebGPU.
