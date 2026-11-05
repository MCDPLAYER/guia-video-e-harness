# Shaders showcase — 15 s, 9:16 (06/10/2026)

**Ideia:** a palavra "isso" é um objeto 3D (texto extrudado, SDF próprio) e é o PORTADOR da peça inteira: cada
"isso" falado troca o material dele (metal líquido → vidro → cristal → obsidiana → nebulosa…), e a troca é uma
transição da própria lib passando por cima do objeto, que não corta nem pula (mesma rotação/posição nas duas cenas).
Mostra o que a lib agrega ao motion flat: material, luz e refração de verdade, em código, quadro a quadro determinístico.

**Áudio:** voz `pt-br-commercial-4` (15,0 s, frases recortadas) · trilha Lyria A em 4 trechos (`bed.py`): intro → parada
com 3 brilhos (= as reticências) → drop no "isso também" (7,70) → acorde final no 1º tempo ≥ 0,25 s após a fala (12,437).
Grade do drop: 0,5921 s. A montagem rápida cai nos tempos 8,884 · 9,476 · 10,068 · 10,661.

| t (s) | causa → efeito | reads (o olho lê primeiro) |
|---|---|---|
| 0,00 | abre preto, "isso" desenhado só pela luz que corre na borda (LightEdge) | o contorno de luz |
| 0,25–1,07 | kicker "agora eu posso fazer" entra palavra a palavra (blur → nítido), câmera empurra devagar | o kicker |
| 1,52 "isso" | kicker sai em 4 quadros · IrisWipe abre do centro do objeto: o contorno vira METAL LÍQUIDO, estúdio claro | o metal |
| 2,74 "isso" | LinearWipe diagonal: o metal vira VIDRO refratando listras laranja/azul em movimento | a refração |
| 4,12 "e isso" | RippleWipe do centro (14 anéis finos, borda seca, cantos limpos em ~9 quadros): vira CRISTAL com o feixe do prisma abrindo arco-íris | o arco-íris |
| 5,67 "e talvez" | música para · NoiseDissolve lento: OBSIDIANA no escuro, câmera empurra (tensão) · kicker "e talvez" | o kicker + o aro |
| 6,62 / 7,06 / 7,21 | os 3 brilhos da trilha = os 3 pontos das reticências, cada um com um reflexo correndo no aro | os pontos |
| 7,41 | anacruse: o objeto recua (antecipação) | — |
| 7,70 "isso" | DROP · BarnDoors abre: NEBULOSA + raios, o "isso" sobe · kicker sai | os raios |
| 8,00 "também" | "também" levanta girando (rotX 75° → 11°) embaixo, mesma largura (bloco justificado) | "também" |
| 8,884 / 9,476 / 10,068 | montagem no tempo: Holográfico (persianas diagonais) → Plástico em estúdio laranja (BlockDissolve) → Água em estúdio azul (RadialWipe) | o material novo |
| 10,661 "um design" | PagePeel descola a página: título CROMADO "design" no estúdio escuro | "design" |
| 11,265 "incrível" | "incrível" levanta girando embaixo (mesmo bloco) | "incrível" |
| 12,437 | ACORDE FINAL: luz varre o cromo, brilhos estelares · "feito em código" entra | o brilho |
| 12,4–15,0 | câmera empurra devagar, acorde soa até o fim | o título |

**Legenda:** calada (a tela escreve todas as frases); o `.srt` leva a fala inteira.
**Banidos herdados:** quique (molas z ≥ 0,7), troca com quadro vazio (toda transição passa sobre conteúdo), órfão.

## Revisor às cegas, rodada 1 (06/10): B (viral de um criador de referência) ganhou. P1 conferidos no 1080

| P1 | conferido | o que fiz |
|---|---|---|
| anel escuro translúcido na ondulação + cantos com listra ~0,1 s | ✅ real | 14 anéis finos, feather .015→.03, progresso ×1,3 com teto: cresce contínuo (3 anéis = anda em degraus, 3 quadros parado) |
| SliceWipe arrasta pedaço duplicado do texto | ✅ real | trocado por VenetianBlinds 30°: era a única troca que MOVE o conteúdo, quebrava a regra "o objeto fica, a máscara passa" |
| legibilidade vidro/listras e "também"/raios | ❌ descartado | no 1080 as duas leem; a miniatura de 216 px é que embaralha |
| plástico e água chapados = "outra peça" | ✅ real | fundo de estúdio iluminado (mesmo componente do metal e do título), tingido |
| 5 tipos de transição = sem gramática | ❌ descartado | é vitrine da lib: variedade de transição É o conteúdo. A gramática que importa (máscara sobre o objeto parado) agora vale nas 9 |
| margem lateral ~9% no fim, "incrível" na coluna dos ícones | ✅ real | título .49→.445 (margem ≥12%) |
| P2 rodapé ilegível no celular | ✅ | 40→46 px, #b9bdcc→#d6d9e4 |
| P2 pausas longas / frase final genérica | ❌ descartado | o roteiro é do autor, e o tempo vem da fala |
| tail lento do `eo` deixa lasca 5–6 quadros (achado meu na tira) | ✅ | persianas ×1,18 e radial ×1,12 com teto |
