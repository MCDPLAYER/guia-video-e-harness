# glass-01 · POC "vidro de UI" (Liquid × Frosted × Blur) · 9:16 1080×1920 · 12 s

Referência: um post de um criador de referência no X (link não incluído) e `glassref/ref.jpg`: três painéis
sobre ilustração. **Clear (iOS)** = vidro limpo com espessura: refração na borda, aumento por dentro, franja RGB, filete
de luz. **Frosted (Concept)** = desfoque + textura de geada que desloca a imagem + grão, tom escuro. **Blur (One UI)** =
desfoque chapado, tom quente, sem espessura.

Motor: shader WebGL2 próprio (sem lib). Fundo = canvas 2D (ilustração `assets/bg.jpg` + conteúdo: título, relógio)
virando textura com mipmap. Vidro = até 8 formas SDF (retângulo arredondado / gota) por camada, fundidas por smooth-min
(gota que se divide e se junta = líquido de verdade). Rótulos e ícones = canvas 2D por camada, POR CIMA do vidro dela
(a camada seguinte refrata os ícones da anterior: a bolha da tab bar aumenta o ícone por baixo).

Gramática: **uma forma, nunca corta** (a gota vira pílula, que vira 3 gotas, que viram 3 cartões, que viram a UI, que
volta a ser uma gota). Mola sem quique (z=1). Sem glow: o brilho é o filete do próprio vidro. Movimento sempre por cima
de detalhe (letra, neon, luzinha), porque vidro parado sobre fundo liso não mostra nada.

| # | tempo | causa → efeito | reads |
|---|---|---|---|
| 1 | 0–0,7 | fundo + "LIQUID / GLASS" gigante já na tela · uma gota de vidro cai de cima do quadro, esticada pela velocidade, e freia sobre GLASS | as letras dobrando dentro da gota |
| 2 | 0,7–1,4 | a gota se abre em pílula (espessura, franja RGB na borda) · chip "Clear · iOS" embaixo | a pílula |
| 3 | 1,4–3,0 | a pílula desliza sobre GLASS (vai, volta): a borda entorta as letras, o miolo aumenta | refração andando |
| 4 | 3,0–3,7 | o conteúdo sobe (título sai por cima) · a pílula estrangula e se divide em 3 gotas (smooth-min) que voam pros lugares | 3 gotas |
| 5 | 3,7–4,4 | cada gota cresce no cartão dela: Frosted largo em cima, Clear e Blur embaixo (layout da referência) · rótulos entram em escada | os 3 rótulos |
| 6 | 4,4–6,0 | ilustração rola por baixo (neon, monitor, luzinhas atravessam os 3 vidros) · 5,0–5,9 Clear e Blur trocam de lugar, se tocam no meio e o vidro se funde por um instante | a fusão na troca |
| 7 | 6,0–6,8 | os cartões viram a tela de bloqueio: Blur → notificação, Frosted → player de música, Clear → dock · relógio 03:21 sobe no fundo | o relógio |
| 8 | 6,8–10,3 | dock com bolha de vidro (camada 2) que pula de ícone em ícone esticando, e aumenta o ícone por baixo (7,4 · 8,4 · 9,4) · barra do player anda · 8,9 o minuto vira 03:22 · 9,0 pause afunda | a bolha |
| 9 | 10,3–12 | tudo se junta numa gota só (smooth-min forte) que para sobre o relógio e aumenta os números · segura | gota sobre 03:22 |

Banidos aqui: quique, glow, fade de cartão inteiro, tempo morto > 0,6 s, quadro vazio na troca.
