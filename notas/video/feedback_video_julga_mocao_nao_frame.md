---
name: feedback_video_julga_mocao_nao_frame
description: "quando recall: como avaliar takes de video IA (Omni/Veo/Flow)? Posso julgar por frame estatico? Regra de prompt enxuto de imagem vale pra video? Como Claude assiste video via Gemini multimodal? Se veredito diverge do humano no teste cego, quem vence?"
metadata: 
  node_type: memory
  type: feedback
---
Avaliando vídeo gerado por IA (Omni/Veo/etc.), **não julgar por frame estático** — o diferencial do vídeo é a MOÇÃO (fluidez, física de líquido/partícula/luz, câmera, continuidade dos elementos no tempo), que frame nenhum mostra. No teste das 3 metodologias de prompt (<cliente-farmácia>, 08/06), o Claude leu frames e cravou "enxuto ganhou"; o **teste CEGO da agência + o autor assistindo** deu o **detalhado (meio-termo/hiper)** como vencedor: "mais detalhes, fluidez e elementos coerentes com mais física".

**Why:** frame estático premia composição parada (o enxuto tinha o fechamento mais limpo); mas o cliente consome o MOVIMENTO. Julgar vídeo por print é medir a coisa errada. Além disso, a regra "prompt enxuto ganha" da [[feedback_image_gpt_workflow]] (nota não incluída) (imagem estática, GPT Image 2) **NÃO transfere pra vídeo**: no Omni, detalhe de moção/câmera/física/transição/timing PAGA — é direção real, não micrometria. Eixos diferentes → ver [[feedback_nao_generalizar_regras]].

**How to apply:** (1) pra escolher entre takes/metodologias de vídeo, **assistir** — e eu POSSO assistir: mandar o vídeo pro **Gemini multimodal** (hoje pelo `agy`, sempre via o wrapper `agy-run.ps1` do autor; [[reference_gemini_codex_cli]], nota não incluída) que ele vê moção + ouve áudio numa tacada (validado 08/06: pegou selo sobreposto, texto exato do rótulo e gagueira que frame+Whisper não pegavam — ver skill `pipeline-multi-llm` seção Multimodal). Frames viram só apoio (continuidade/artefato pontual). De preferência, ainda cruzar com o humano (teste cego). (2) Em prompt de VÍDEO, detalhar moção/física/câmera/transição é bom; não importar a regra "enxuto" da imagem estática. (3) Quando meu veredito divergir do humano que assistiu, o humano vence — corrigir docs na hora. Operacional em [[project_gflow]] (nota não incluída; ver a skill `gflow`).
