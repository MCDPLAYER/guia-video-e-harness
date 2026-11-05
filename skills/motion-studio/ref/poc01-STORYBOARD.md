# POC-01 — "Uma forma, nunca corta" (busca de cena → clip pronto)

Formato: 1080×1080 · 60 fps · 12,0 s · loop perfeito (último frame = primeiro, cursor parado nos dois) · 120 BPM (beat = 0,5 s)
Gramática (roubada de um viral de UI motion de um criador de referência, não o conteúdo): UMA forma que se transforma, o cursor CAUSA toda mudança,
fundo cinza-quente claro, componentes preto/branco + 1 acento (#FF5A36), springs com overshoot mínimo,
câmera reenquadra cada estado, motion blur real por subframes, zero tempo morto.

| # | tempo | evento (causa → efeito) | reads (o que o olho lê, em ordem) |
|---|---|---|---|
| 0 | 0,0–1,0 | pill "Buscar cena" com ponto vivo pulsando no beat; cursor chega e CLICA no beat 2 | pill → cursor → clique |
| 1 | 1,0–2,5 | pill vira campo branco (morph + conteúdo sai com blur); digita "gojo vs sukuna"; ENTER no beat | campo abriu → texto sendo digitado |
| 2 | 2,5–3,5 | campo cresce PRA BAIXO (topo fixo, a busca fica no lugar = continuidade) e vira lista; 3 linhas entram em cascata; hover na 1ª; clique | lista → 1ª linha destacada → clique |
| 3 | 3,5–6,5 | a miniatura da linha VOA e vira a capa do player (elemento compartilhado); player escuro toca; cursor arrasta o scrubber (timecode rola com blur) | capa → título → barra tocando → arrasto |
| 4 | 6,5–8,5 | clique "Cortar" → player vira barra de corte com filmstrip; arrasta a alça de saída; tooltip com duração rolando 00:12→00:07 | barra → alça → número caindo |
| 5 | 8,5–10,0 | clique "Exportar" → a barra COLAPSA no botão (círculo), câmera empurra; anel enche em degraus no ritmo; check estoura em laranja no beat | círculo → progresso → CHECK |
| 6 | 10,0–11,0 | círculo abre em toast "Clip pronto · 00:07" | toast |
| 7 | 11,0–12,0 | toast volta a ser a pill inicial; cursor volta ao ponto de partida | loop |

Gates: (1) stills dos 8 estados → contact sheet · (2) strip dos morphs mais difíceis (1,0 / 3,5 / 8,5) a cada frame · (3) filme inteiro + áudio.
