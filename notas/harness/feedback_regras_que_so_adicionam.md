---
name: feedback_regras_que_so_adicionam
description: "quando recall: por que o feed/as pecas do cliente foram ficando poluidas se cada regra do DECISOES.md estava certa? Como um doc de REGRAS ATIVAS que so cresce por acrescimo degrada a peca? Anexar mood/referencia visual impede a peca de inchar (nao — mood ancora o COMO, brief decide o QUANTO)? Preciso de teto de densidade e como escrever sem virar formula/layout canonico engessado? Qual a ordem de corte quando selo, telefone, CTA, logo e bullets competem pelo mesmo espaco? Como registrar correcao do cliente sem transformar tudo em obrigatorio universal? O que e additive bias / subtraction neglect aplicado a regra de marca?"
metadata: 
  node_type: memory
  type: feedback
  modified: 2026-08-05T20:50:45.578Z
---

Num sistema de produção visual com **canvas limitado** (card de feed, story, capa), regras ativas que **adicionam elemento visível** — e que são tratadas como obrigatório universal, sem escopo, prioridade, substituição ou teto — tendem a empurrar a peça pra poluição, **mesmo com cada regra individualmente correta**. É a mecânica do doc de regras, não a qualidade delas.

**Why:** caso medido no `<cliente-farmácia>` (05/08/2026; nota de aprendizados do cliente não incluída). A agência reprovou o feed de agosto — "posts muito poluídos", "distante do mood original" — comparando com o feed de junho. Peça típica passou de **3 blocos** de informação pra **7+**.

Rastreando bloco a bloco, **as regras explicam ~2 dos 7**: o rodapé institucional com os dois telefones (regra "citou cidade, cita o número", que eu apliquei na *arte* quando o pedido não dizia onde) e a logo renderizada no design além da que já vinha no rótulo do produto (regra "logo sempre anexada", que é sobre o *anexo do prompt*). **O resto — os 4 bullets, os 4 passos, os 4 quadrantes — foi deriva de briefing minha, que nenhuma regra exigiu.**

Essa divisão importa e eu errei nela primeiro: minha versão inicial culpava as regras por tudo, e o contraponto do Codex derrubou. **As regras criam o PISO obrigatório; o operador empilha o miolo em cima.** Só a soma estoura, então só teto sobre a peça inteira resolve.

**Por que referência visual não salva:** o mood-master do feed bom estava anexado em **todas** as peças ruins. **Mood ancora o COMO** (cor, luz, tipografia, clima); **o brief decide o QUANTO.** Nenhuma imagem de referência barra um prompt que lista quatro bullets e um rodapé. Isso é a versão "de conteúdo" do princípio que já estava em [[feedback_image_gpt_workflow]] (nota não incluída) — *instrução só pega se for restrição verificável* —, e mostra o buraco dele: a regra verificável cobria a **composição**, ninguém tinha posto teto na **quantidade**.

## How to apply

1. **Classifique toda regra nova** ao gravar: *adiciona · remove · substitui · limita · prioriza · condiciona*. Um doc só de "adiciona" é o sintoma.
2. **Regra que adiciona obrigação visual declara escopo e o que ela desloca** quando houver colisão. "Sempre X" sem dizer onde X mora (arte? legenda?) é o que produziu o rodapé em toda peça.
3. **Teto é ORÇAMENTO, não fórmula.** Fórmula diz "use esta estrutura" (foi vetado no `<cliente-farmácia>` como "5 layouts canônicos" e seria vetado de novo). Teto diz "não ultrapasse esta carga sem remover, mover ou justificar". No `<cliente-farmácia>` (decisão daquele cliente): **3 blocos de texto por card; não coube = é carrossel**.
4. **Teto exige ordem de corte**, senão só adia a decisão. Lá: telefone → CTA → bullets → kicker; compliance (selo/Resp. Téc.) só sai se a peça não for de produto; mensagem principal, foto e logo (1×) nunca saem.
5. **Conte antes de escrever o prompt** — blocos, logos, selos, CTAs, telefones. O corte é no brief, não no re-roll.
6. **Compare a referência também por QUANTIDADE**, não só por mood: quantos blocos tinha a peça boa?

## ⛔ O que esta nota NÃO autoriza

**Não é licença pra deixar de registrar correção do cliente** — esse erro é pior e já custou caro ([[feedback_brand_profile_e_espelho]] (nota não incluída); "regra que existe em UM caminho não é regra"). A formulação correta: **nenhuma correção do cliente é apagada; mas toda regra ativa que adiciona obrigação visual precisa ter escopo, prioridade e regra de colisão.** Correção pode virar regra *condicional* ou *substitutiva* em vez de obrigatório universal.

## Onde a tese NÃO vale (limites, pós-contraponto Codex)

- Regras que adicionam **critério** sem adicionar **elemento visível** (ex.: "sem claim inventado") — não ocupam espaço, não competem.
- Peças **editorialmente densas por natureza** (infográfico, tabela comparativa, peça regulatória onde densidade é o preço correto).
- **Carrossel e formatos sem canvas escasso** — o teto é do card; o carrossel existe justamente pra receber o excedente.
- Causalidade segue **hipótese operacional forte, não conclusão testada**. Teste que fecharia: montar a mesma peça com o doc de regras de junho, julho e agosto, mantendo briefing e referência constantes, e contar os elementos obrigatórios de cada versão.

## Nome que isso já tem

**Additive bias / subtraction neglect** — a tendência de resolver problema adicionando em vez de remover ([artigo na Nature, 2021](https://www.nature.com/articles/s41586-021-03380-y)). Primos úteis: *scope creep* (acúmulo de requisito sem controle de mudança), *WIP limit* do Kanban (o teto explícito como mecanismo), *dívida técnica* (regra acumulada sem refatoração vira cruft). Se for explicar pro usuário, "additive bias" é o termo que ele acha na busca.
