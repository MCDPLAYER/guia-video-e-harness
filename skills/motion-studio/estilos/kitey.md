# Estilo KITEY: palco escuro, janelas 3D, a câmera faz o corte (vídeo-ensaio do canal)

**Peças aprovadas:**
- **Vídeo-ensaio 16:9 do canal do autor** (`<video-ensaio-01>`, v3.2, 81 s, aprovado pelo autor em 19/10). O código do
  motor não está neste repo; o diagnóstico da v1 está em `ref/gramatica-de-edicao.md` e os retornos v2 → v3.2, abaixo.
- **Short 9:16** no mesmo motor, aprovado pra postar em 19/10.
  - Voz original recortada do take do vídeo longo + clipes, sem legenda: a tela escreve a fala.

**Referência:** 3 vídeos do criador que dá nome ao estilo, medidos (cortes, câmera por fluxo ótico, voz × música separadas
por demucs) e assistidos inteiros em 16/10/2026.

## Quando
- Vídeo-ensaio do canal com narração em 1ª pessoa sobre clipe de anime, tom reflexivo ou bem-humorado.
- Diferença pro 32Bits:
  - Kitey = câmera contínua num palco, ~1,5–2 s por plano;
  - 32Bits = cenário novo a cada ~1 s.

## Números (3 vídeos medidos)
- **Cortes:** 11–23/min, plano mediano 1,5–2,1 s. Varia de 1 a 32 dentro do mesmo vídeo: o minuto "parado" é a câmera voando (planos de 8 a 65 s).
- **Zoom** em 36–72% dos planos · **fala** 152–209 palavras/min · **música** 11–16 dB abaixo da voz, sem automação de volume visível.
- **Tempo de tela:** ~45% janelas no palco · 28% tela cheia · 15% texto cinético · 12% prints, fotos e memes.

## Gramática
- **O quadro é um PALCO:**
  - papel escuro amassado;
  - os clipes entram como janelas 3D inclinadas 5–15°, com balanço lento que nunca para.
- **A câmera faz o corte:** viaja entre as janelas (pan, whip, empurrão) no meio da frase. Entre as viagens ela deriva na direção da próxima chave e flutua: "a câmera voa e para" foi retorno do autor (tempo parado 24,5 s → 3,0 s).
- **Tela cheia é RESPIRO, não padrão:**
  - entra quando a cena precisa ser sentida, sem texto em cima;
  - tela cheia no filme inteiro foi **reprovada** ("tenebroso… bagunça visual", 19/10);
  - a cena inteira entra num momento escolhido (na peça aprovada: UMA, de ~6 s).
- **Texto é cenário:**
  - monta palavra por palavra no tempo da voz, colado na borda de uma janela, nunca solto no centro;
  - minúscula branca, palavra-chave em CAIXA ALTA salmão/rosa; eco gigante a ~10% por trás; marca-texto animado sobre prints;
  - tipografia mista: condensada → serifa na virada do roteiro.
- **A geometria muda a cada 15–20 s:** 16:9, 4:3 com borda VHS, PIP, folha de jornal, Super 8 (sépia, vinheta oval, som de projetor = memória).
- **Quebra cômica a cada 30–40 s:** GIF, meme, foto banal, palavrão seco. Nenhum trecho de 40 s sem uma.
- **Registro:** abertura "sufoco + freio" (~1 corte/s por 10 s e parada seca numa palavra); a 2ª metade pode ficar limpa (virada sem falar); o fim pode ser uma canção que sobe ao volume cheio depois da última palavra.

## Música e SFX
- Uma Lyria **por etapa retórica** (pizzicato → cordas tensas → piano), não uma faixa que "evolui".
- **Corte seco** da música na piada.
- O sobe e desce vem do arranjo; a voz fica 11–16 dB acima. Reta ficou "tímida"; +11 dB na pausa leu "épico forçado". Aprovado: onda leve (−2,5 dB sob a fala, +4 no respiro ≥ 0,35 s, +4 nos 2,5 s antes de cada golpe).
- SFX gravado de banco, literal pro substantivo (caixa registradora, lápis), 15–25/min nos trechos densos: `<pasta-de-midia>\efeitos sonoros` via `sfx_org.py`.
  - **Variar** ("mesmice em loop" foi retorno do autor): papel diferente por janela, whoosh do tamanho da viagem, riser → impacto → downer nas quebras, o mesmo arquivo com pico e tom variados (v3: 119 sons de 59 arquivos).
  - **Nenhum SFX acima da voz** (RMS 400 ms): o golpe que ia 6 dB acima foi pra 5 abaixo; o mais alto aprovado ficou 2,7 dB abaixo.

## Banidos
- Clipe em tela cheia com legenda embaixo por mais de ~8 s seguidos.
- 40 s sem quebra.
- Uma faixa só de trilha.
- Papel vazio entre janelas: a próxima aparece antes de a cena sair.

## Pegadinhas do motor
- O rodízio de whoosh em `EVENTS` depende do ÍNDICE de cada chave da câmera (`CK`): pra tirar uma viagem, troque-a por uma chave que segura no lugar (`d: 0`), sem apagar.
- Cena inteira = chave `d: 0` + `lock` na janela (trava e endireita) + sombra no canto do texto. A janela que ficaria no caminho da volta se esconde, e a próxima aparece antes de a cena sair.
- Mudou o tempo de algo? events → sfx → apague os `.done` da chave anterior até a seguinte → render → finish.
