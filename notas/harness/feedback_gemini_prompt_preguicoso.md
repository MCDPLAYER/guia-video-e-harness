---
name: gemini-prompt-preguicoso-afirma-errado
description: "quando recall: por que prompt curto no Gemini/agy da resposta ruim? Ele omite ou inventa quando nao sabe? Preciso escrever prompt longo pra Gemini mesmo em tarefa simples? O --effort resolve? Por que o Codex aguenta prompt curto e o Gemini nao? Quando o Codex ve video de verdade e quando ele so mede?"
metadata: 
  node_type: memory
  type: feedback
  modified: 2026-09-02T19:08:00.411Z
---

**Prompt preguicoso no Gemini/agy e PROIBIDO — ele nao entrega menos, ele AFIRMA errado.**

**Why:** o modelo raciona esforco sozinho, pelo quanto o prompt parece cobrar — e o `--effort` NAO controla isso. Medido 2026-09-02, mesmo video de 8s, mesmo `gemini-3.8-flash-high`, mesma flag, so o prompt mudando:

| | thinking tok | total | tempo | resultado |
|---|---|---|---|---|
| "Descreva esse video" | **795** | 29,6k | 24,5s | ignorou o audio inteiro, nao viu o ponto piscando nem o flash, errou a duracao, e **afirmou "deslocamento linear"** quando era ease-in-out |
| prompt cobrando 5 itens | **5.878** | 176k | 167,5s | **5/5 perfeito** — reconstruiu os parametros exatos de geracao (880 Hz, frames 103-105, 6 Hz, x=40->539) |

7,4x mais raciocinio pelo mesmo effort. A capacidade esta la; ele so nao gasta se nao for cobrado. **O harness novo (agy 1.1.24) NAO consertou isso** — ver [[reference_antigravity]] (nota não incluída).

O modo de falha e o caro: ele **nao diz "nao consegui apurar"**, ele preenche com o palpite mais provavel e entrega com cara de certeza. Omissao da pra notar; afirmacao errada passa. Casa com [[feedback_inferencia_sem_prova]] (nota não incluída).

**How to apply:** em TODA rota que passa por agy/Gemini — enumerar o que quero item a item, exigir precisao (numero, intervalo, posicao), e **mandar explicitamente dizer "nao consegui apurar" em vez de chutar**. Prompt curto so em tarefa cujo erro eu detecto de graca. Comparativo: o Codex degrada bem menos sem prompt bom (no mesmo teste ofereceu o audio sem ninguem pedir e acertou a duracao) e o custo dele quase nao move com prompt melhor (22,1k -> 24,9k, +13%, contra +495% do agy).

---

## FRONTEIRA: mensuravel x julgamento (a divisao que realmente decide o tier)

**FATO medido no mesmo dia:** o Codex tem **ZERO percepcao nativa de video e audio**. Ele acertou 5/5 no mesmo teste escrevendo INSTRUMENTO: `ffmpeg -af silencedetect` pros bipes, `scipy.io.wavfile` + threshold de amplitude pra medir o audio, `cv2.VideoCapture` lendo os 192 frames pra rastrear a posicao do quadrado e provar a aceleracao. Scripts ficaram no disco (`audio_detail.py`, `full_tracker.py`, `analyze_video.py`). No teste anterior, de outro clipe, ele extraiu frames a 2fps e montou **folha de contato** `tile=4x2` — e foi atras da skill `media-tools-local` sozinho pra descobrir que tinha ffmpeg na maquina.

**RACIOCINIO — a regra que sai disso:** instrumentar responde o **MENSURAVEL**, nunca o **JULGAMENTO**.
- "quantos bipes / que frequencia / em que segundo / acelera ou nao" -> script resolve, Codex ganha (e por isso o token dele mal sobe: o trabalho pesado roda na maquina, nao no contexto).
- "a mocao convence / o corte respira / a transicao ficou brega / o audio ta sujo" -> **nenhum script responde**. Ali o agy e insubstituivel. Reforca [[feedback_video_julga_mocao_nao_frame]].

⇒ Roteia por TIPO DE PERGUNTA, nao por "quem e mais inteligente".

**Economia (numeros do autor, 02/09):** assinatura Codex R$120/mes x Google AI Pro estudante R$35/mes = **agy 3,4x mais barato pelo mesmo 5/5** no prompt elaborado. Comparar TOKEN entre planos de assinatura fixa nao quer dizer nada — token so vale como proxy de quao rapido a cota drena. Casa com [[feedback_custo_mora_no_contexto]] (o gargalo e COTA, nao dinheiro).

⚠️ **NAO APURADO, e e o numero que decide:** se chamada de `agy` queima os 1.000 creditos/mes da AI Pro ou roda fora desse contador, e qual o tamanho real do pool. Nao da pra ler da CLI. Nao afirmar nos dois sentidos ate medir.

⚠️ **Limite do experimento (o autor apontou, procede):** 6 rodadas, tarefas sinteticas. O que NAO depende de amostra e mecanismo — o racionamento por prompt e a instrumentacao do Codex (scripts no disco provam). O **empate de qualidade continua n=1** por tipo de tarefa. E o teste favorecia o Codex por construcao: tudo nele tinha resposta numerica objetiva; julgamento estetico separaria os dois, mas nao tem gabarito pra pontuar. Ver [[feedback_padrao_sucesso_vs_ruido]] (nota não incluída).
