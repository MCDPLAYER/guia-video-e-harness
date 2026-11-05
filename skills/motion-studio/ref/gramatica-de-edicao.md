# Gramática de edição (vale pra qualquer estilo)

Extraída da edição de um criador de referência de vídeo-ensaio (`<criador-de-referência>`) e testada num vídeo do canal do
autor. No fim: a estética medida desse criador e o diagnóstico de uma intro que ficou só "ok" (o ritmo estava certo; o
quadro, não).

## Cardápio de jogadas

**Cardápio, não gate.** Pega a jogada quando o bloco pede; nenhuma é obrigatória.
- Provado no minuto 6 de um ensaio do canal (`<video-ensaio-02>`): o autor aprovou em 23/10/2026 ("aprovado, curti
  bastante") e nenhuma lição atrapalhou.
- Fonte: a transcrição de um vídeo do criador de referência + uma nota de moldes de editores do autor (não incluída).
- O que o estilo já cobre não se repete aqui: respiro, transição pelo objeto, fundo escurecido e SFX por evento estão em
  `estilos/kitey.md` e `estilos/32bits.md`.

| jogada | como | no teste (min 6) |
|---|---|---|
| **Transição rara, com motivo** | Corte seco é o padrão. Transição só quando a cena PEDE: entrar num lugar, mudar de plano, quebrar algo. Se dá pra dizer o porquê numa palavra, entra. | só 4 no minuto: portal, subida, rasgo, máscara |
| **Zoom com razão** | Cada zoom diz algo: empurrão = tensão ou atenção, mergulho = entrar no assunto, empurrão lento = peso emocional. Zoom de enfeite tira a força do zoom que importa. | empurrão na porta, mergulho, zoom no buraco, empurrão lento no desespero e na gota crescendo |
| **Todo elemento com função** | Antes de pôr na tela: o que ele faz pela fala? Quando entra um novo, o anterior escurece ou recua, e o olho vai pro novo sem disputa. | o anterior escurece quando o novo entra |
| **Respiro** | Bloco sem elemento novo depois de um trecho denso, pra frase importante assentar. | bloco 7 |
| **O olho vê, o ouvido confirma** | Um som por objeto que entra. A trilha corta SECA na frase de virada; abafa (passa-baixa) quando a cena mergulha; atmosfera baixa por baixo; troca de trilha casada com um som de transição. | um som por objeto; música para seca em "Esse é o problema" e abafa no mergulho |

## Gap em aberto: o visual segue o TEXTO, não o TOM da voz

O autor, 23/10: "o ultimo gap que eu não sei como resolver é a intencionalidade da voz, as vezes eu falo algo mais
risonho ou sério e o mood não acompanha, eu falo gaguejando ou mais "certo" e nada casa".

- **Hoje:** o mapa fala→tela sai da transcrição, que só tem palavra. O jeito de falar (rindo, sério, hesitando, firme)
  não entra.
- **Hipótese, ⏳ não medida:** marcar o tom por trecho antes de montar e casar a moção com ele. Rindo =
  elástico/overshoot. Sério = parado, corte seco. Gaguejando = tremor/repetição. Firme = seco e centrado. A marcação
  pode vir de um modelo que ESCUTA o áudio, ou do próprio usuário numa passada rápida.
- Não abrir rodada sem o usuário pedir.

## Estética do criador de referência (medida em 16/10/2026)

Medido por script (cortes, câmera, voz × música) e assistido inteiro por um modelo de vídeo; paleta e textura
conferidas nas folhas. O vídeo ensina identidade visual, então o que interessa é o que ele FAZ na tela.

- **Números:** 20 cortes/min · plano mediano 2,5 s · zoom em 73% dos planos (mediana 10%) · música −40,3 dB sob a voz
  −21,5 (~19 dB abaixo) · 189 palavras/min.
- **Cor da peça, não do clipe:** base preto + branco + vermelho sangue, com blocos de amarelo (xadrez) e magenta quando o
  assunto muda. A cabeça do apresentador em halftone P&B recortada amarra tudo. Papel branco nos cartões de capítulo;
  serifa clássica sobre o preto pra "modo sério".
- **Ponteiro é objeto:** mãos em halftone P&B apontando, não seta vetorial.
- **Moldura com eco:** clipe emoldurado a ~70% da tela, com o mesmo clipe atrás em tela cheia, desfocado e escurecido.
- **Nada congelado:** stop-motion a 6–8 fps nos recortes e rabiscos (o 32Bits usa 12).
- O resto (textura em 2 camadas, 3 camadas + rack focus, entrada de 5–12 quadros com overshoot curto, 2 fontes, parada
  seca da trilha na ironia) é igual ao 32Bits: ver `estilos/32bits.md`.

## Diagnóstico: ritmo dentro da faixa, quadro pobre (16/10/2026)

A intro de um vídeo-ensaio do canal (`<video-ensaio-01>`, v1) ficou "ok". Autor: "parecia sem substância e
profundidade… tem algo ali, por mais que não surpreenda". Medida com o mesmo script das referências:

| | Cortes/min | Plano mediano | Planos com zoom | Música sob a voz | Música nas pausas |
|---|---|---|---|---|---|
| Intro (nossa, v1) | 22 | 2,3 s | 87% | 18 dB abaixo | **sobe 11 dB** |
| Kitey (3 vídeos) | 11–23 | 1,5–2,1 s | 36–72% | 11–16 dB abaixo | fica onde está ou cai |
| 32Bits | 34 | 0,9 s | 53% | 15 dB abaixo | sobe 3 dB |
| criador de referência | 20 | 2,5 s | 73% | 19 dB abaixo | sobe 8 dB |

Corte, câmera e volume estavam dentro da faixa deles: **mexer na tesoura não resolve.** O que as 3 referências têm e a
intro não tinha:
1. **Quadro construído.** Na intro, quase todo o tempo era o clipe em tela cheia com faixa preta e legenda embaixo. Nelas,
   o clipe é um objeto dentro de uma cena montada (janela 3D no palco, moldura com o próprio clipe desfocado atrás, TV,
   folha de caderno); a tela cheia é ~28% do tempo e entra como respiro. O olho via o mesmo tipo de quadro por 80 s.
2. **Cor da peça.** A intro usava a cor de cada clipe (bege, azul lavado, cinza). Nelas, o fundo é da peça (blocos
   saturados, ou preto + branco + 1 cor) e o clipe entra dentro dele.
3. **Textura e profundidade.** Nenhuma camada por cima nem por trás (papel, halftone, sombra, desfoque de fundo): o "sem
   profundidade" era literal, a imagem tinha um plano só.
4. **Texto como cenário.** Legenda pequena e fixa embaixo + palavra carimbada por cima do clipe, contra texto montando
   palavra por palavra colado nos objetos, com a câmera viajando de um bloco a outro.
5. **Um registro só.** 80 s no mesmo tom grave. Sem quebra (foto banal, meme, palavrão a cada 30–40 s), o grave perde
   peso e vira "sem substância".
6. **Trilha que incha.** Subir 11 dB em cada pausa leu como "épico forçado".
7. **SFX sintético.** Os deles são gravados e literais (lápis, caixa registradora, papel, obturador): a "organicidade".

O refazer (mesma locução, mesmos clipes, composição Kitey) foi aprovado em 19/10. Os retornos e os consertos medidos
estão em `estilos/kitey.md`.

## Ao comparar com a referência
- **Modelo de vídeo que "assiste" erra contagem:** contou pan como corte ("24,4 trocas/min nos dois") e errou a
  contagem de SFX. Use pra descrever; conte na folha e na tira.
- O que se confirmou na comparação do `<video-ensaio-01>` contra 81 s de um vídeo Kitey: lá a imagem ocupa a tela
  inteira ~80% do tempo (nossa: 22%), com o texto EM CIMA mudando de fonte/cor/tamanho; 23 cortes secos contra 8; mundo
  real (tela do YouTube, debate na TV, matéria de jornal) e GIFs de reação pequenos como piada; e o tom da voz varia
  ~30% mais (desvio 5,5 × 4,2 semitons, maior em 7 de 8 janelas de 10 s). Copiar o "tela cheia o tempo todo" num teste
  de 0–20 s foi reprovado pelo autor ("ficou tenebroso… uma bagunça visual"): o aprovado mistura os modos (ver
  `estilos/kitey.md`).
