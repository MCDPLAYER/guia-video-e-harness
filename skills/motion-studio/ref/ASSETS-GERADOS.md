# Imagem e vídeo gerados dentro do motion: MENU, não regra

O autor, 30/09/2026, depois da POC-02: *"não era um teste de quem é melhor, cada um é melhor no seu eixo"*. Isto é a lista
do que tem à mão; a escolha continua criativa, peça a peça. Pode combinar, alternar ou usar um só.

## O eixo de cada um (exemplos do autor)
- **Código (esta skill):** o que precisa sair CERTO e re-gerar custa caro: dado, número, relação entre números,
  texto exato, UI, fluxo. Ex.: talking head feito no Omni que começa a falar de dados → o Omni teria que re-gerar
  a pessoa junto até acertar o gráfico; o motion entra por cima ou intercalado.
- **Imagem Nano (Flow, 0 crédito):** objeto, lugar, textura, produto real. Ex.: vídeo com galeria de fotos de
  produto → gera as imagens e encaixa, em vez de gastar tempo vetorizando.
- **Clipe Omni (Flow, crédito, só com OK):** movimento físico e passagem orgânica. Ex.: transição entre duas
  imagens que não são "elementos" de código → o Omni faz; pessoa falando; papel saindo da impressora.

## Como encaixar (medido na POC-02, peça 9:16 de 14 s pro `<cliente-app>`)
### Imagem
- Pedir 9:16 e só o objeto em "white seamless" quando ele entra por `mix-blend-mode:multiply` num campo liso (o
  fundo some); cena completa quando a foto é janela de tela cheia.
- Jeitos que funcionaram: multiply no campo · janela revelada por limpador de serrilha · foto em cartão que abre
  até a tela cheia. A câmera empurra na foto com a mesma mola do código.
- **Cor:** a imagem traz a luz dela (a macro veio bege quente numa peça off-white). Nivelar por filtro no motor
  (`saturate`/`brightness`) antes do filme; "fundo fora da paleta" foi P1 do revisor.

### Clipe Omni
- **Frame inicial = a mesma foto que já está na peça:** o quadro 0 bate e a troca foto→vídeo não pula.
- Mesmo com câmera travada e 1 movimento no prompt, o Omni **desfez o movimento** no meio (o papel subiu até 3,25 s
  e voltou) e ignorou "blank". → Folha do clipe a cada 0,5 s antes de cronometrar; usar o trecho monotônico,
  acelerado se precisar (usei 0–3,1 s a 1,66×).
- Download: `<GUID>.tmp` (mp4 inteiro), 720×1280 a 24 fps.
- No motor: quadros em JPEG (`ffmpeg -q:v 2`) pré-carregados na promessa do `ready`, num canvas com a mesma
  transformação da foto; **entre dois quadros de 24 fps, mistura pela fração** (sem degrau a 60 fps).
- **Troca vídeo→código:** medir por quadro o retângulo e a cor do objeto no clipe; o elemento de código nasce em
  cima, na cor medida, dissolvendo em 0,15 s (o que o clipe imprimiu vira o conteúdo do código); o clipe congela
  só quando o código já cobre.
- **Objeto do clipe que vira tela cheia não ESTICA:** sai na geometria da cena, se solta (rasga, sobe, ganha
  sombra) e só então voa até a tela, em proporção quase fixa e com o conteúdo preso. Retângulo alargando por cima
  da impressora foi o P1 nº 1 do revisor ("colado por cima").

## Custo medido (⏳ reconferir a partir de 12/2026)
Imagens: ~6 min de ida ao Flow pra 4. Omni: 12 créditos por 8 s 720p, < 70 s de geração; o tempo caro foi o
encaixe no código, não o Flow. Render 14 s 9:16 `modo=leve`: ~2,4 min por versão.
