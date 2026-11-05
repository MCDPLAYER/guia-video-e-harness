---
name: feedback_subagent_critico_padrao
description: "quando recall: rodo o subagent crítico antes de entregar peça de cliente? preciso perguntar antes de rodar o crítico? o crítico vale pra prompts+copy pré-geração ou só pra peça já gerada? o crítico pega CONTRADIÇÃO entre trechos (prazo/preço/promessa que se contradizem)? posso confiar cego no 'crítico passou'? crítico mais caro substitui sandbox/gate humano em script destrutivo? o crítico está engessando / pesando a mão / matando o espaço criativo? como escrever o BRIEFING do crítico? crítico só veta ou pode apontar oportunidade de fazer MAIS? o crítico deixou a peça SECA / certa mas sem graça? o crítico pode reescrever ou só apontar?"
metadata: 
  node_type: memory
  type: feedback
  modified: 2026-09-23T13:44:02.536Z
---

Antes de entregar QUALQUER peça de cliente em produção (cards/carrosséis/stories/ads — `<cliente-farmácia>`, óticas, `<cliente-entidade-lojista>`, `<cliente-sindicato>`, `<cliente-B>`, `<cliente-ótica>`, `<cliente-K>`, canal do autor), rodar o **subagent crítico por PADRÃO e SEM perguntar** ao usuário (preferência do autor). Vale inclusive quando a entrega é o **pacote de prompts+copy pré-geração** (não só a imagem já gerada) — o crítico valida brand/compliance/copy/ativos ANTES do usuário gastar geração.

**Why:** o autor (03/07/2026) mandou "torne padrão nos próximos, sem eu pedir, um subagent crítico antes de você fazer as entregas". Ele tem olho treinado e cansou de eu OFERECER ("quer que eu rode o crítico?") em vez de já entregar validado. O crítico pega o que eu auto-certifico com bar baixa: ex. "blindagem" fora do veto OAB da advocacia ([[feedback_oab_publicidade_advocacia]], nota não incluída), claim ANVISA no `<cliente-farmácia>`, mood de ótica trocado (`<cliente-G>` verde × `<cliente-H>` carvão), CTA-botão, idioma errado.

**How to apply:** ao montar entrega de cliente, ANTES de apresentar → crítico nas 5 dimensões (🔴 **corrigido 17/07: o crítico é EXTERNO — Codex. NUNCA outro Claude/`Agent tool`**, que era o que esta linha mandava — auto-atestado não é garantia, [[feedback_orquestracao_multi_llm]]. O `Agent tool` serve de **executor/varredura**, não de crítico) (padrão de marca / copy-idioma / integridade visual / critério honesto PUBLICÁVEL-DIGERÍVEL-REFAZER / ativos obrigatórios); corrijo o que ele achar; SÓ ENTÃO entrego. Nunca perguntar "quer que eu rode?". Pular só em POC/exploração declarada. Causa-raiz fixada nas skills `image-gpt` (§Subagent crítico) + `criativos-rede-social` (PASSO 7). Liga com [[feedback_image_gpt_workflow]] (nota não incluída), [[feedback_postura_autonoma]] (nota não incluída).

## Pegadinha + como conferir (10/07/2026)

**Crítico passou ≠ peça coerente.** O crítico externo é forte em *violação de regra dada* e *erro objetivo isolado* (achou "31 de junho", achou preço R$2,50 que era R$3,00, achou produto errado no card). Mas ele avalia trecho a trecho: **contradição entre dois trechos distantes passa batido**.

- **Gatilho:** peça que repete número, prazo, preço ou promessa em pontos diferentes (legenda × card × CTA; carrossel multi-slide).
- **Contraexemplo mínimo (medido):** "Resultados garantidos em 7 dias" + "uso contínuo mínimo é de 90 dias", a 3 linhas de distância. `gpt-5.5` e `gpt-5.6-sol` (ambos `high`) marcaram **cada frase isolada** como promessa proibida pela ANVISA — **nenhum dos dois viu que elas se contradizem**.
- **Como conferir:** depois do crítico passar, reler a peça inteira caçando **um** eixo por vez: prazos batem? preço bate com a quantidade? o produto citado é o mesmo do começo ao fim? Coerência narrativa é gate do orquestrador (eu), não do crítico.
- **Corolário (contraponto Codex 10/07):** crítico mais caro **não substitui controle de execução** — em script destrutivo o que protege é sandbox/diff/gate humano, não modelo melhor.

Evidência e método do teste → [[feedback_orquestracao_multi_llm]] (seção 10/07) e [[reference_gemini_codex_cli]] (nota não incluída).

## Peça REESCRITA depois da crítica volta pro crítico (27/07/2026)

**Gatilho:** o crítico aprovou o pacote, aí uma peça é **substituída por outra** (troca de tema, correção de eco, pedido do autor). O reflexo é entregar — "já passou pelo Codex". **Não passou: aquele texto é novo.**

- **Medido:** pacote de 27/07 passou no Codex (8 bloqueios corrigidos). Depois trocamos **4 peças inteiras** por anti-repetição. 2ª rodada de crítica nas 4 novas: **nenhuma passou limpa** — 4 bloqueios + 8 ajustes, incluindo um absoluto factualmente errado ("os impostos nunca são zero"), uma oposição técnica falsa que fabricava problema pra vender serviço ("impulsionar não é campanha" — impulsionar *é* criar anúncio), e garantia de resistência de produto sem lastro (CDC 36-38).
- **Bônus do 2º passe:** ele pegou **erro meu de edição** — o prompt da `<cliente-H>` ainda mandava fazer "peça sobre a COR da armação", tema que eu tinha acabado de descartar. Troca parcial deixa entulho do conceito velho no prompt; o crítico é quem enxerga.
- **Como conferir:** trocou peça depois da crítica? Roda 2ª rodada **só nas trocadas** (prompt dizendo quais são novas e por quê) — é barato e pega o entulho.
- **E não engula tudo:** recusei "tirar o hex da cor da marca e as negações" do prompt `<cliente-farmácia>` (é o frasco-base canônico da marca e negação ativa funciona no GPT Image 2, [[feedback_image_gpt_workflow]]) e a reescrita da voz em juridiquês nas óticas. Crítico corrige **risco**, não redefine a voz do cliente — aceitar cego vira bula.

## 🔴 O outro lado da mesma moeda: o crítico CONFIRMA quando o briefing EXPLICA (15/09/2026)

> **O autor:** *"uma coisa é você perguntar 'o objetivo de que tais elementos pareçam tais coisas foi
> atingido' — nessa pergunta você já manda sua intenção, ele olha o vídeo já entendendo o que precisa
> entender, ele recebe justamente a peça que falta."*

**A regra:** o briefing do crítico pode dizer **o que verificar** e **como responder**. Nunca **o que
a peça significa**. 🔴 **A pergunta que entrega a intenção não pode medir se a intenção chegou.**

- **Medido no loop de vídeo (15/09):** a lente que fazia o "teste mudo" — a que deveria ser o
  espectador limpo — recebia, antes de assistir: *"NÃO há rosto humano: o rosto é GEOMETRIA"* e
  *"o quadro é a LENTE de um óculos; o vidro trinca com o tempo perdido"*. Assistiu com o gabarito.
- ⚠️ **E o dano é pior que "ele confirma o que já sabe": a informação EXPLICA O DEFEITO COMO INTENÇÃO.**
  Um agente avisado de que "o vidro trinca" lê um brilho amarelo desalinhado sobre o texto como parte
  da estética da trinca. O defeito ficou 15 versões na tela e nenhuma lente o chamou de defeito.
- **O tensionamento é real e tem saída:** sem linha dura o Gemini fica preguiçoso; com linha dura
  explicativa ele "segue o baile". ⇒ **linha dura no FORMATO da resposta** (percorra do início ao fim,
  marque cada trecho CASOU / NÃO CASOU / NÃO ENTENDI, "não sei" é resposta válida) — **nunca no conteúdo**.
- **How to apply:** em toda entrega com intenção declarada (peça de vídeo, card, roteiro, nome, layout),
  **pelo menos um crítico recebe ZERO contexto** — não sabe o produto, a estrutura nem o objetivo — e é
  perguntado só o que viu/entendeu. É o único que consegue responder "isso se explica sozinho?".
  Os outros continuam com o protocolo completo. Exemplo montado: `_loop/no3/lente-cega.md` no projeto
  Remotion. Liga com [[feedback_card_se_explica_sozinho]] (nota não incluída).
- ✅ **Medido em CARD, 16/09/2026 — e com dois limites que o nome "zero contexto" esconde.** Duas
  recusas de cliente no mesmo dia (`<cliente-sindicato>`, `<cliente-ótica-3>`) tinham passado por mim e por um juiz que lia a
  justificativa de cada peça. Leitor sem contexto (Codex + Gemini, só o que está na tela, recusadas
  misturadas como controle): no `<cliente-sindicato>` **os dois travaram no mesmo ponto do cliente** e deram 4/5 ⇒
  **travou = cai, a nota não decide**. No `<cliente-ótica-3>` deram 5/5 e "fala com o cliente final" ⇒ **clareza não
  mede LADO**; o defeito só apareceu na pergunta *"o que você faria depois?"*. Procedimento:
  `_clientes/_CRITICO-PROTOCOLO.md` §7-bis · gate: skill `gates-da-peca` §5.4.

## 🔴 O crítico engessa quando o BRIEFING engessa — auditoria de 10 sessões (26/08/2026)

O autor reclamou que o crítico "engessa e pesa a mão, sem espaço criativo". Auditei as 10 últimas sessões
de posts do dia (13/08→26/08, 110 peças) e o instinto dele tem número — mas **a causa não era o Codex,
era o briefing que EU escrevia pra ele**. O protocolo canônico agora mora em
`<pasta-do-drive>/obsidian/_clientes/_CRITICO-PROTOCOLO.md` (não incluído neste repo).

- **Medido:** 99 achados classificados = 55 REFAZER · 43 REESCRITA · **1 ajuste fino** (ele não tem
  marcha baixa). **349 verbos subtrativos contra 6 aditivos** — nenhum "aprofunda"/"arrisca". Eu acatei
  **84%** (81 de 97): crítico com 84% de acatamento não aconselha, veta.
- 🎯 **O número que decide:** motivos reais de recusa do cliente no período = **5 por peça rasa/vazia**
  (`<cliente-B>` 14/08 *e* 24/08, `<cliente-ótica-2>` 14/08, `<cliente-farmácia>` 17/08 e 19/08) contra **0 por peça arriscada
  ou falsa**. A régua estava calibrada contra um risco que nunca aconteceu.
- 🧪 **Contra-teste no mesmo dia:** a peça `<cliente-D>` de 26/08 à tarde saiu com briefing diferente (gate de
  voz + 3 marchas + "não reescreva") → **DIGERÍVEL**, 4 ajustes, publicada. Mesmo Codex, mesmo modelo.
  **O briefing é a variável, não o modelo.**
- 🟢 **O papel dele mudou de definição (o autor, 26/08):** *"não é só necessariamente vetar, ele tá lá pra um
  terceiro ponto de vista, que pode ser pra identificar o problema ou enxergar uma oportunidade de fazer
  mais"*. O produto dele é DETECÇÃO nos dois sentidos — defeito **e** oportunidade desperdiçada.
  ~~Continua valendo que ele **aponta, não redige**.~~ 🔴 **Caiu em 23/09, ver seção abaixo.**
- **How to apply:** todo briefing de crítica leva as 5 travas do protocolo — (1) três marchas
  REFAZER/AJUSTAR/LIMPO com **cota de no máximo 3 REFAZER por pacote**; (2) **gate da voz** (peça segura e
  morta é defeito e ele tem que dizer); (3) **via positiva** obrigatória de olhar, não de preencher;
  (4) **conserto mínimo** — "trocar o tema inteiro" é último recurso; (5) origem nomeada.
- ⚠️ **A régua de eco era o motor do engessamento:** varrer "histórico completo", entre clientes, dentro de
  legenda e em peça descartada faz de toda peça um eco. Fechada em 26/08 → mata só em **mesmo cliente
  <30 dias** ou **frase literal repetida**; entre clientes, declara e segue. Liga com
  [[feedback_regras_que_so_adicionam]] e [[feedback_regua_nao_mentir_glamurizar]] (nota não incluída).
- 📏 **A janela de 30 dias tem lastro, não é chute:** dos 34 ecos com antecedente datável nas 10 sessões,
  **31 (91%) estavam a menos de 30 dias** — mediana **5 dias**, 21 deles a menos de uma semana. O Codex
  recontou por conta própria e chegou a 13-14 de 17 ainda detectados. **Nenhum eco comprovadamente caro é
  liberado.**

## 🔴 O crítico entrega a versão MELHOR, não a mais segura — SECA é achado (23/09/2026)

> **O autor:** *"tá certo e entendível, mas tá seco, sem substância, glamour — nada. O crítico não tem só o
> dever de achar os problemas e sim também sugerir algo MELHOR — não mais 'seco pra ficar seguro'."*

- **Caso:** um card (QR code rasgado). Cinco rodadas crítico ↔ leitor sem contexto, **todas certas
  no defeito**, e cada conserto **tirou** algo (o número, a unidade, o objeto). Saiu com 5/5 de clareza e
  seco. Nenhum gate media secura, porque todos mediam se a peça estava **certa**.
- **A regra:** ele escreve a proposta, e ela só vale se resolver **trocando por peso igual ou maior**
  (fato, mecanismo, objeto, imagem). Proposta que resolve tirando = o defeito voltando por outra porta.
  Gate da secura no template; pergunta 6 no leitor sem contexto (*"pararia o scroll?"*). Conserto mínimo
  = mínimo no **escopo**, nunca na riqueza. Canônico: `_CRITICO-PROTOCOLO.md` §2 · skill `gates-da-peca` §4.
- ⚠️ **E eu errei pro outro lado na mesma hora:** "mais rico" virou engenharia (QR escaneável de verdade no
  card). O autor: *"a criatividade eu quero no tema"* (preferência do autor). O peso que entra é o que o leitor **vê**.

## 🔬 O crítico no modo novo criticou o PRÓPRIO protocolo — e ganhou o teste (26/08/2026)

Ideia do autor: *"chama o codex nesse novo modo dele pra ver se o que fizemos foi eficiente mesmo — se a
resposta for ruim já sabemos que temos que melhorar, se for boa já ganhamos um upgrade, win win"*.
Veredito: **"pronto pra rodar sexta: NÃO"**, com 3 REFAZER + 5 AJUSTAR, todos procedentes.

- 🔴 **O furo mais grave era meu:** a cota de "máximo 3 REFAZER" fazia a **quarta afirmação falsa** virar
  "AJUSTAR — sai hoje". Ele: *"a frase 'não é limite de qualidade' não muda o efeito operacional: é
  exatamente um limite de qualidade."* **Fix: hard stop (falsidade, compliance, risco físico, guardrail
  explícito) nunca entra em cota; a cota vale só pra causa-raiz editorial, e reprovação repetida pelo
  mesmo motivo agrupa num achado sistêmico.** Cota sem essa separação é remédio matando o paciente.
- ❌ **Ele pegou um erro factual meu:** escrevi que as 4 óticas dividem "o mesmo ramo, na mesma cidade".
  **Não dividem praça nenhuma** — `<cliente-ótica-3>`, `<cliente-G>`, `<cliente-H>` e `<cliente-ótica-2>` ficam cada uma num estado. O risco entre elas
  é **repetição de portfólio**, não saturação de público. Eu inventei a justificativa
  ([[feedback_inferencia_sem_prova]] (nota não incluída) de novo). Quem divide cidade é o grupo de clientes da `<cidade>`.
- 🧩 **"Subtema" era largo demais pra marcha fatal** — série, campanha e continuação repetem subtema sem
  repetir tese. **A unidade do eco é TESE ou MECANISMO CENTRAL.** E a régua que eu declarei "unificada"
  não estava: `<cliente-ótica>` media "mecanismo", `<cliente-farmácia>` media "produto/eixo".
- 🔒 **Poda derrubou uma razão viva:** a quarentena do checklist do `<cliente-ótica-2>` existia por **diferenciação
  de portfólio**, não por eco temporal — eu converti em quarentena de 30 dias e ela perdeu o motivo.
  **Território de marca não vence com o calendário.** Exceção cross-cliente vale só se estiver escrita no
  `DECISOES` e **não expira**.
- ⚖️ **Ele contestou o diagnóstico, com razão:** *"acatamento do próprio autor não mede precisão
  independente, e zero recusas por falsidade depois do filtro pode significar que o filtro funcionou"*.
  E citou contra mim o `<cliente-B>/DECISOES.md:76` — eu escolhia a saída rasa *"porque é a que não dá
  trabalho"*. **A causa é conjunta:** briefing subtrativo + pacote com defeito real + meu acatamento de
  84% amplificando os dois. Consertar só o briefing conserta um terço.
- 🟢 **Duas melhorias que vieram da VIA POSITIVA** (a trava nova já pagou): **(1)** cada peça entra com uma
  linha *"descoberta: o que o leitor não sabia · lastro: qual fonte sustenta"* — peça que não preenche as
  duas metades volta pra mim **antes** do crítico, atacando o "raso" na origem; **(2)** ledger de 4
  rodadas (achado · marcha · acatei? · motivo · reação do cliente), porque taxa de acatamento e contagem
  de verbo medem o **meu** comportamento, não a **precisão dele**.
- **How to apply:** protocolo grande **passa pelo crítico antes de virar cânone** — inclusive protocolo
  sobre o próprio crítico. Custo: uma rodada. Retorno aqui: um furo grave, um erro factual e duas
  melhorias que eu não tinha.
