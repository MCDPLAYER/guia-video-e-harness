---
name: feedback_efeitos_omni_ambicioso
description: "quando recall: que nível de efeito/VFX propor no Omni/Flow pro usuário? por que 'CapCut é mais seguro' me travou e ele achou o efeito conservador/fraco? o fantasma sumiu porque pedi sutil? texto/número no Omni funciona ou erra? quando ser ousado vs cauteloso num efeito de vídeo? por que eu recuei DE NOVO tendo essa regra escrita? o usuário disse 'para de medo' / 'muito conservadorzinho' — o que eu fiz de errado? posso abrir exceção pra essa nota nesse caso específico?"
metadata:
  node_type: memory
  type: feedback
  modified: 2026-08-26T19:40:41.382Z
---

> Preferência do autor: em efeito criativo ele quer explorar o TETO do motor. Isso é gosto dele; o que é medido (texto curto sai legível no Omni, prompt "sutil" faz o efeito sumir, o mecanismo de inventar risco) vale para qualquer um.

Com o autor, em **efeito/VFX no Omni/Flow o default é AMBICIOSO** — proponho a versão ousada primeiro, o aviso técnico vira rodapé, não freio.

Piloto <cliente-sindicato> V1 (22/07): pedi fantasma "translúcido/sutil/leve brilho/se dissipa" e o Omni fez ~nada — o efeito sumiu justo no vídeo cujo tema era fantasma. E desencorajei texto ("melhor CapCut"), mas duas cartelas curtas de número (formato "NN MIL" / "MENOS DE N MIL") saíram **limpas e legíveis** no Omni. Feedback do autor: *"foi mais conservador nesses efeitos do que eu queria, já fizemos coisas mais ambiciosas que deram certo"*.

**Why:** apliquei a lente "seguro/conservador" — que é de **gasto e risco real** (fal/API paga/publicação) — num contexto **criativo**, onde o autor quer explorar o TETO do motor. Ali ousadia não é risco, é o ponto; efeito tímido subestima o Omni e entrega menos do que ele encomendou. Erro de eixo, igual [[feedback_nao_generalizar_regras]]. Conecta [[feedback_postura_autonoma]] (nota não incluída), [[reference_omni_api_oficial]] (nota não incluída).

**How to apply:**
- Efeito criativo → descrever **forte, opaco, ocupando o quadro, movimento nítido**; não "névoa etérea sutil".
- Texto/número **curto** no Omni **funciona** — não empurrar pro CapCut por reflexo. Só a cartela LONGA / acento-Ç é que erra mais (aí sim CapCut).
- "Isso pode errar" entra como rodapé, não como trava.
- A cautela conservadora fica pro que é **gasto/risco/publicação**, não pra estética.

🔴 **A NOTA EXISTIA E NÃO ME IMPEDIU — 3 recuos na MESMA sessão (<cliente-I>, 17/08).** Não foi um deslize: foi o meu default aparecendo três vezes seguidas, e nas três o autor teve que puxar.
1. Propus **voz off** em vez da personagem falando com a câmera, alegando que lipsync de personagem IA "denuncia o fake" — *"não tem base isso e o lipsync sai perfeito, já fizemos excelentes vídeos com bom retorno"*.
2. Empurrei o **preço pro CapCut** "por segurança". A linha *"não empurrar pro CapCut por reflexo"* **já estava escrita nesta nota** — e eu a contornei criando uma exceção mental: *"texto curto sim, mas preço é crítico demais"*.
3. Escrevi o VFX do re-edit como brilho decorativo, sem o valor na tela — *"prompt muito conservadorzinho, quero o valor aparecendo pow, para de medo rapaz"*.

**Why — o mecanismo, que é o que interessa:** a regra não falha por eu esquecer dela; falha porque eu **abro exceção pra ela em cima de um risco que eu mesmo invento** ("preço é crítico", "lipsync denuncia"). É a mesma máquina de [[feedback_inferencia_sem_prova]] (nota não incluída): a cautela chega vestida de argumento técnico e eu não peço a prova dela. E o custo não é só a peça tímida — é o usuário gastar o turno dele me destravando.

**How to apply:** quando eu for propor a versão contida de algo criativo, **nomear o risco em voz alta e perguntar se ele é medido**. Se a resposta for "não testei", a versão ambiciosa é a entrega e a ressalva vira rodapé. E **exceção a esta nota exige evidência, não intuição** — "esse caso é diferente" é exatamente como ela é burlada.

🔴 **4º recuo (<cliente-D>, 26/08) — e este passou POR BAIXO da nota, porque não foi uma proposta tímida: foi a LETRA MIÚDA do prompt.** O autor pediu preço + logo como elemento no Omni. Eu **fiz** o efeito — e no mesmo parágrafo escrevi *"crisp flat 2D overlays, sharp edges, **no 3D, no perspective, no warping, no motion blur**, and they never bend around the man or the shelves"*. Saiu correto e legível, e ele: *"meio covarde esses efeitos"*.

**Why — o mecanismo NOVO:** a nota acima me pega quando eu **proponho** a versão contida. Não me pegou aqui porque eu propus a versão ambiciosa e **a matei na cláusula negativa**. Cinco proibições numa linha só, nenhuma delas pedida por ninguém, todas contra deformação que **eu não tinha visto acontecer** — é a mesma máquina de risco inventado, só que escondida na sintaxe do prompt em vez de na conversa. Efeito "que não erra" e efeito "que não aparece" saem pela mesma porta.

**How to apply:** antes de disparar VFX, **reler o prompt e contar as negativas**. Cada `no X` tem que ter um defeito OBSERVADO atrás dele — se eu não vi acontecer, sai. Trava de forma (`flat`, `sharp edges`, `never bend`) é decisão estética disfarçada de segurança técnica: só entra se o usuário pediu aquele registro. O default é o oposto — deixar o motor propor profundidade, peso e movimento no gráfico, e podar depois se poluir.

**Estrutura que o autor quer em vídeo multi-take (<cliente-sindicato> 24/07; preferência do autor, com o motivo técnico de emenda junto):** **1 efeito por take**, não o efeito só no take "anotado" no doc — o doc lista 1 efeito por VÍDEO (o conceito), mas cada take dividido precisa do seu, senão fica pelado. Moderação aqui é por **ritmo** (5 efeitos em 6s polui), nunca por "o modelo não aguenta". E **cada take termina LIMPO** (efeito surge e se resolve antes do fim) pra emendar no próximo — **só o último take** do vídeo pode fechar com o efeito na tela. Prop-swap é exceção (contínuo, ver [[reference_omni_api_oficial]]).
