# Estilo UI: uma forma que nunca corta (produto, app, SaaS, fluxo, dado)

**Peças aprovadas:**
- POC-01 (1:1, ganhou às cegas de um viral de UI motion de um criador de referência na v3);
- reels do `<cliente-app>` (9:16, 42 s, aprovado pelo autor em 28/09);
- VSL do `<cliente-app>` (16:9, 4 min);
- série do `<cliente-farmácia>`, peças da `<parceria-musical>` e curso do `<cliente-app>`.

**Motor e storyboard:** `ref/poc01-index.html` (não incluído), `ref/poc01-STORYBOARD.md`. Os storyboards das peças de cliente saíram do repo; o que eles ensinam está nas seções abaixo (ver também GUIA-VIDEO.md).

## Quando
- Cliente com produto, tela, fluxo ou número.
- O padrão pra peça de cliente quando o brief não pede outra estética.

## Gramática
- **Uma forma, nunca corta.** Toda troca de estado tem um **portador**: algo do estado A VIRA algo do estado B.
  - Ex.: linha → player; seleção → chip → anel; check → selo → ponto.
- **Nunca interpolar a cor de fundo por baixo de conteúdo visível.**
  - Uma máscara nasce do ponto da causa, cresce até passar da forma, e o fundo troca seco por baixo.
  - **O que NÃO vira o próximo estado sai no clique, em ~4 quadros**, antes de a máscara passar por cima.
  - O conteúdo do estado novo chega JUNTO com a máscara.
  - O portador entrega no MESMO quadro em que o alvo aparece (um conjunto tipo `ITEMSET` liga um e desliga o outro).
- Um acento de cor atravessa a peça inteira. Os dados batem de ponta a ponta (a mesma duração do corte aparece no toast).
- Substrato: grão estável por quadro, sombra em camadas, blur de saída.
- Enquadramento: estados largos em 65–80% da largura; a câmera aproxima nos estados pequenos.
- Cursor em arco de bézier (`MOVES`), com `press`/`hold`. **Todo estado tem uma causa visível.**

## Banidos
- **Easing com quique, explosão de partículas, glow, gradiente em chrome de UI.**
- **Tempo morto:** mais de 0,6 s sem evento novo.
  - Locução não conta como evento. Quando a fala segura, a câmera dá um passo por palavra-chave, alternando perto/aberto (~+.12/+.03 de escala).
  - Passos pequenos que só somam leem como câmera parada (P1 `<cliente-farmácia>` V3, 07/10).
- **Troca com quadro vazio no meio:** fade, ou voo entre estações sobre chão vazio. O próximo anuncia, com sombra ou borda, antes de o anterior sair.
- **Cor intermediária suja:** cinza entre preto e branco.
  - Véu `multiply` chapado pra escurecer um take lê como filtro roxo.
  - Noite = `backdrop-filter` brightness/saturate + vinheta, borda macia por máscara CSS radial (`mask: radial-gradient(…)`).
  - Nenhum ancestral com blend/filter/opacity, que quebra o backdrop (V3, 07/10).
- **Órfão:** selo ou score que fica sozinho depois da troca.
- **Texto centralizado sobre fundo em gradiente:** é o default do modelo sem referência.

## 9:16 com tela de celular
- Em estado de celular, a câmera empurra (1,5–1,7) até a linha narrada ser a maior coisa da tela.
- Um celular inteiro a 42% da largura foi P1 do revisor no 1º case.
- Reels de cliente: cada lugar da história = uma cor de fundo (troca pela máscara da causa); loop com o último quadro = o quadro 0.

## VSL longa (16:9): um mundo gigante, câmera voando, sem corte
- **Estrutura:** cada ato é uma estação que monta quando a câmera chega e desmonta quando sai.
  - 3D (plano em pé, sombra projetada, voo inclinado 30–60°) no voo; 2D chapado onde a voz explica.
- **Pacing:** até ~12 s de voz, depois um respiro de 2–3,4 s em que a ilustração PAGA o que foi dito, sem informação nova. A fala ancora os beats narrados; no respiro a música manda (o pico cai NO quadro da batida, ataque de 2 quadros). Beat simbólico no código: início da palavra no json do whisper e tempo 1 de compasso mais perto na grade medida da trilha.
- **Banidos na VSL** (além dos da UI): slide de bullets, grade de ícones de banco, número de resultado ou depoimento.
- **Legenda:** faixa y 930–1010. Conteúdo em repouso acaba acima de y ~900 (a câmera sobe; 3 P1 do revisor).
- **Mundo 3D em CSS: sombra é objeto.**
  - Sombra em z .3 pinta por cima do que está em z 0, inclusive no quadro em que ele POUSA (`max(z, 1)`).
  - Com o objeto deitado e a câmera longe (escala .25), até de z .6: subir o objeto até 4 px.
  - A sombra esmaece quando o objeto dobra ou sobe, e some com ele. Sombra sem objeto é órfão.
  - Rótulo no chão com objeto em pé: o objeto vem pra frente.
- **Câmera no mundo:**
  - pan linear com escala em log desvia do alvo (interpolar no espaço do zoom, `zin`);
  - plano em pé visto de câmera oblíqua sobe na tela (subir o z do alvo);
  - chave `eio` é parada total, então a câmera só para depois que o objeto sai;
  - texto grande perto de um empurrão: a câmera segura até ele sair, e só então empurra;
  - **o que a borda cortaria no repouso sai antes** (nunca fica fatiado);
  - a câmera pousa em conteúdo que já está lá, nunca no vazio.
- **Motor de estações** (cada ato num `t0–t1`):
  - o `t1` esconde os portadores, então reexibir um elemento passa pela estação dele;
  - cache de estilo com 2 estações precisa de chave composta;
  - ato que CONSOME portadores deixa o mapa esparso no recuo;
  - DOM gigante (~7000 px) trava o Chrome: plano ×10 menor e escala; o chão vira pintura no canvas.
- **Leitura de objeto:**
  - o que cai entra de fora do quadro (z alto) ou com fade de 2 quadros, nunca brotando na borda;
  - slot ou selo vazio ganha miolo opaco (senão o chão aparece através);
  - portador branco sobre cartão branco parece glitch.
