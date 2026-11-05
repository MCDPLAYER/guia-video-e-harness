# Estilo VIDRO LÍQUIDO (UI de vidro + material 3D)

Primo da UI: herda a gramática e os banidos de `ui.md`. O que muda é o material.

**Peças:**
- **Vidro de UI:** `ref/glass-01/`, aprovado pelo autor em 16/10. `vivo.html` é a versão arrastável. O STORYBOARD tem os 9 beats e as 3 variantes:
  - **Clear iOS:** espessura, refração na borda, aumento no miolo, franja RGB, filete de luz;
  - **Frosted:** desfoque + geada que desloca + grão, tom escuro;
  - **Blur One UI:** desfoque chapado, tom quente, sem espessura.
- **Material em título, abertura ou fecho** (vidro, cromo, cristal, metal líquido em texto 3D): lib `shaders`, `ref/shaders-lib/LEIA.md` (MIT). ⏳ Showcase aprovado em 06/10, sem peça de cliente ainda.

## Quando
- Abertura premium, app com estética iOS/One UI, título que precisa de "material".
- Fecho de marca.
- Não serve pra peça longa inteira: o vidro precisa de detalhe por baixo pra aparecer.

## Gramática
- **Uma forma, nunca corta, feita de líquido:** gota → pílula → 3 gotas → 3 cartões → UI → gota.
  - As formas SDF são fundidas por smooth-min, então o vidro se divide e se junta de verdade.
- **Vidro só se lê em movimento por cima de detalhe:** letra, neon, luzinha. Parado sobre fundo liso, não mostra nada. Role a ilustração por baixo.
- **Camadas:** o rótulo e o ícone de cada camada ficam POR CIMA do vidro dela. A camada seguinte refrata a anterior (a bolha da tab bar aumenta o ícone).
- Mola sem quique (z=1). O brilho é o filete do próprio vidro, nunca glow.

## Banidos (além dos da UI)
- Fade de cartão inteiro.
- Vidro parado sobre fundo chapado.

## Pegadinhas
- **O fundo do vidro vai em data URL.** Imagem por file:// suja o canvas e o WebGL recusa.
- Fundo = canvas 2D (ilustração + conteúdo) virando textura com mipmap. Até 8 formas SDF por camada (WebGL2 próprio, sem lib).
