---
name: feedback_regra_vira_campo_fixo
description: "quando recall: escrevi a regra em maiúsculas no brief e o modelo violou mesmo assim, por quê? como impedir que o modelo escreva alt de foto de stock como se fosse a loja real? quando a instrução não adianta e eu tenho que travar o campo? por que 4 modelos diferentes cometeram o mesmo erro?"
metadata: 
  node_type: memory
  type: feedback
  modified: 2026-08-13T18:28:53.948Z
---

# Regra que briga com o instinto do modelo não é regra — é campo fixo

Medido em 13/08/2026 no piloto de sites do `<cliente-sindicato>`. **4 de 4 modelos** que entregaram site
completo cometeram exatamente a mesma violação, apesar de o brief proibir em maiúsculas na
restrição nº 1:

```
alt="Interior da loja de calçados"
alt="Prateleiras e exposição de calçados na loja"
alt="Interior de loja de calçados com prateleiras"
```

Foto de banco de imagens descrita como sendo a loja real do cliente. Erraram MiniMax M3
(NIM), Gemini 3.5, Gemini 3.6 e Haiku 4.5 — famílias e preços diferentes. O único que não violou
usou `alt="Vitrine"`, `alt="Ambiente"` — e só porque entregou um site raso de 3 seções.

**Why:** descrever uma foto de interior de loja como "interior da loja" é o comportamento
**correto** de quem escreve texto alternativo. A regra estava pedindo ao modelo que se contivesse
contra o próprio instinto certo — e por isso perdia sempre, do modelo caro ao grátis. Instrução
mais enfática não resolve; **tirar a decisão da mão dele resolve**.

**How to apply:**
- 🔴 **O teste:** se a regra depende de o modelo *se conter* contra o que ele naturalmente faria
  bem, ela está na camada errada. Vire campo fixo, formato obrigatório ou template.
- Formato que ficou no brief: `alt="<objeto> — imagem ilustrativa"`, com exemplo negativo
  explícito ao lado. Exceção: foto **enviada pelo próprio cliente** pode ser descrita como o que é.
- Mesma família de solução do `"uma equipe preparada"` no lugar de `"nossa equipe"` (regra do
  `<cliente-sindicato>`, 13/08): o que a **licença ou a verdade** exige, o template escreve; o que é **gosto**,
  o modelo decide.
- Sinal de que você está na camada errada: a mesma violação aparece em modelos de linhagens
  diferentes. Um modelo errando é modelo; quatro errando é o brief.
- ⚠️ Não confundir com regra que barra **default ruim** do modelo (inventar telefone, desenhar
  estrela ao lado da nota) — essas funcionam como instrução, porque o modelo não tem instinto
  legítimo a favor delas. A distinção é: o comportamento proibido é *bom* fora deste contexto?
  Se sim → campo fixo. Se não → instrução basta.


## ⚠️ O outro lado: campo fixo também vira LICENÇA (26/08/2026)

Campo fixo garante que a regra seja cumprida — e **por isso mesmo** libera o resto da peça de cumprir
o objetivo dela.

**O caso:** em 14/08 a agência reprovou um card por não dar contexto, e o conserto foi *"nomear o assunto
num kicker"*. Certo naquele caso. Eu transformei em **campo fixo do template** — e aí, com um lugar
garantido pro assunto, **passei a escrever headline enigmática**, porque o contexto "já estava lá em
cima". Em **26/08** o autor derrubou a leva inteira: **10 de 11 peças com kicker**, e o kicker carregando
o assunto enquanto a headline carregava a charada. *"Ninguém lê o mini header."*

**Why:** o campo fixo satisfaz a **letra** do conserto e desonera a peça da **intenção** dele. Quanto
mais confiável o campo, mais o resto relaxa.
**How to apply:** ao criar campo fixo, escrever junto **o que ele NÃO dispensa** — e um teste que mede a
intenção, não a presença do campo. Aqui virou o **teste do kicker coberto**: tape o campo fixo; o resto
da peça ainda cumpre o objetivo? Detalhe em [[feedback_card_se_explica_sozinho]] (nota não incluída) §5.

## Relacionado
[[feedback_nao_generalizar_regras]] · [[feedback_regua_nao_mentir_glamurizar]] (nota não incluída) · [[project_sites_empresas_ouro]] (nota não incluída) · [[feedback_orquestracao_multi_llm]]
