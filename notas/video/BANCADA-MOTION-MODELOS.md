# Bancada de motion: o teste padrão para medir modelo de IA em motion design

> Desde 19/10/2026, por decisão do autor, este é **o** prompt para testar qualquer modelo novo em motion.
> Mesmo prompt, mesmo contrato e mesmo render: assim o resultado de hoje dá para comparar com o de daqui a 6 meses.
> **Os resultados ficam só na tabela no fim desta nota** (lista viva num arquivo só).

## O prompt (criativo, LITERAL, não corrigir nem a grafia)

```
faça um motion desing de 25s vendendo você mesmo como modelo de IA, mostrando que voce é um mestre do desing
```

O prompt é curto de propósito: mede o que o modelo faz **sozinho**, sem brief, sem referência e sem gramática.
Para medir quem executa bem um brief bom, existe o outro teste, o da POC-02 (`<pasta-do-projeto>\POC-02-modelos\BRIEF.md`, não incluído).

## Contrato técnico (vai colado embaixo do prompt, só para dar render)

```
---
Contrato técnico (só para o diretor conseguir renderizar; não muda o pedido acima):
- Entregue UM arquivo `index.html` nesta pasta, tudo inline, sem rede (sem CDN, sem fonte web, sem imagem externa). Use fontes do sistema Windows.
- Palco 1920×1080 no canto superior esquerdo (body margin 0). Duração 25,0 s, 60 fps.
- O quadro é função pura do tempo: `window.seek = t => new Promise(r => { render(t); requestAnimationFrame(() => requestAnimationFrame(r)); })` e `window.ready = true` quando estiver pronto. Sem CSS transition/animation, sem timer, sem estado carregado entre quadros (canvas e WebGL valem, desde que desenhem a partir de t).
- Com `?render` na URL não toca sozinho; sem parâmetro, toca em loop em tempo real.
- Quem renderiza é o diretor (Playwright + Chrome, motion blur por subquadros). Você não roda navegador nem render. Não faça perguntas; é uma tentativa só. Termine com uma linha dizendo o que entregou.
```

Cópia pronta para usar: `<pasta-do-projeto>\bancada-motion\PROMPT-BANCADA.md`.

## Como rodar

1. **Pasta isolada fora do projeto**, uma por modelo (ex.: `<pasta-isolada>\<modelo>-NN`), com só o `PROMPT.md`.
   O modelo não vê o trabalho dos outros.
2. **Uma chamada**, pela via oficial de cada CLI (os `.ps1` e `.py` citados são wrappers do autor, não incluídos):
   - Gemini pelo `agy-run.ps1`;
   - OpenCode com `--dir` na pasta isolada;
   - Claude como subagente NOVO com `model` explícito;
   - Codex pelo `codex-run.ps1`, **com as skills da máquina desligadas**: o Codex carrega `~/.agents/skills` e o Terra leu a `motion-studio` antes de escrever (27/10). Gere a cópia do wrapper com `_harness\codex_sem_skills.py <pasta>` e rode por ela.
3. Anotar:
   - tempo de relógio;
   - quantas chamadas e por quê (timeout, vazio, `length`);
   - nível de raciocínio.
   - ⚠️ O DeepSeek free no OpenCode **gasta todo o teto de 32k raciocinando e não escreve o arquivo**. Ele precisa de `OPENCODE_CONFIG` com `limit.output` em 128000 (POC-02).
4. **Render** com `<pasta-do-projeto>\bancada-motion\_harness\render.py <pasta> --dur 25 --w 1920 --h 1080`.
   - Usa 4 subquadros, nvenc qp 18 e 2 navegadores. **Um render por vez.**
   - Um quadro que quebra fica registrado e não derruba o render: "renderizou limpo?" é uma medida.
5. **Olhar** com o `pack.py`: folha de 1 em 1 s e tiras de 30 quadros nas trocas.
   Para conferir um conserto sem gastar o render inteiro, use o `_harness\stills.py <pasta> <saida.jpg> t1,t2,...`, que tira quadros soltos direto do html.
6. **Opcional, pipeline "modelo faz → Opus melhora":** o Opus pega o `index.html` do modelo e só tem a tarefa de melhorar, pelo método da skill `motion-studio`: render, folha, tira, corrige.

## Resultados (a lista viva mora AQUI)

| Data | Modelo · via · nível | Chamadas | Tempo | Render limpo? | Nota / o que fez | Pasta |
|---|---|---|---|---|---|---|
| 19/10/2026 | Gemini 4 Argon · agy · high | 1 (o envelope voltou `ERROR stream interrupted` DEPOIS de gravar o arquivo inteiro: conta como sucesso) | 960 s · tokens in 62k / out 51k / think 20k | ✅ limpo (render 184 s) | Reel de 6 atos em canvas, 2.188 linhas: áurea + Bézier, bento suíço, tipografia cinética, escultura 3D com lente de vidro, física de mola, manifesto. **Ambicioso no conteúdo, quebrado no acabamento:** wipe 1→2 INVERTIDO (quadro preto + card órfão + corte seco), persianas 4→5 com cara de glitch, portal 5→6 = card preto voando sobre o texto, paleta que vira neon (lima/ciano/roxo), rótulos de 11–12 px, cor da cena 4 inválida (`rgba()` em string `rgb()`). **Opus melhorou** (1 passada, ~25 min): 3 trocas viraram faixa laranja que varre, paleta creme/tinta/laranja, texto +35%, cena 2 e final adiantados para não entrar vazio, CTA maior; estrutura do Argon mantida | `<pasta-do-projeto>\bancada-motion\01-argon\` (`argon/` × `opus/`) |
| 19/10/2026 | Claude Sonnet 5.5 · subagente Claude Code (`general-purpose`, `model: sonnet`) · padrão | 1 (testou numa simulação de canvas em Node, sem navegador) | 510 s · 149,7k tokens · 3 tool uses | ✅ limpo (render 244 s) | Reel de 7 cenas em canvas, 840 linhas: abertura com símbolo, tipografia (Bézier), roda de cor com contraste AA ao vivo, espiral áurea, editor de easing com stagger, código que vira card de player, fecho com CTA. **Limpo e legível, mais calmo e mais curto que o Argon.** Defeito de raiz: toda cena começa a montar DEPOIS da troca, então cada wipe revela tela vazia (cor → composição = creme liso; persianas tipo → cor = painéis pretos com cara de glitch). Também: fundo da cena de cor com 4 neons somando lama, glow nas bolas, card do código nascendo como quadradinho, HUD e rótulos de 13–18 px, CTA pequeno. **Opus melhorou** (1 passada, ~20 min): cena nova já chega montando (adiantada 0,55 s; composição 1,2 s), soco de escala 1,12→1 na entrada e recuo da cena velha, borda da troca em faixa grossa, empurrão lento contínuo em toda cena (sem tempo parado), fundo de cor numa luz só, sem glow, card sobe inteiro com giro, texto miúdo +20–30%, CTA maior; estrutura do Sonnet mantida. Render do refino 442 s (PC em uso) | `<pasta-do-projeto>\bancada-motion\02-sonnet\` (`sonnet/` × `opus/`) |
| 19/10/2026 | Claude Opus 5.5 · subagente Claude Code (`general-purpose`, `model: opus`) · padrão | 1 (só conferiu a sintaxe, sem navegador) | 653 s · 150,3k tokens · 4 tool uses | ✅ limpo (render 371 s) | Reel de 5 atos em canvas, 760 linhas, com uma ideia de roteiro: o pedido "deixa isso bonito." digitado → Grid (colunas, régua, retângulo áureo) → Ritmo (kerning fechando, escala modular) → Cor (5 amostras viram barra 60/30/10, contraste real AAA/AA, o 30% escuro vira o fundo) → Forma (o mesmo conteúdo vira cartaz, app, revista) → assinatura "✳ Claude". **O mais elegante e o único com fio narrativo; também o mais calmo e o mais vazio.** Paleta creme/tinta/argila coesa, serifa grande. Defeitos: 4 trocas passam por tela vazia (entrada em 3 s = um ponto sozinho; Cor entra em branco; Forma = ~0,4 s de preto chapado; assinatura = creme com uma estrela pequena), texto do HUD em ~12 px, tela inicial em branco. Sem refino (o teste era ele sozinho) | `<pasta-do-projeto>\bancada-motion\03-opus\opus\` |
| 27/10/2026 | GPT-6.1 Terra · Codex CLI 0.163 (`codex-run.ps1`, conta Plus, sem custo extra) · high | 1 válida. Houve uma 1ª, descartada: o Codex carregou as skills da máquina e ele leu a `motion-studio` antes de escrever (47 s, guardada em `_viu-skill/`). Refeita com as skills desligadas | 37 s · 21,3k tokens (o Codex não separa entrada/saída/raciocínio) · 3 comandos | ✅ limpo (render 399 s, PC em uso) | Canvas de **79 linhas (6 KB)**: o menor e o mais rápido da bancada, e é um **slideshow**. São 6 cartazes suíços de 4,1 s cada, com fundo chapado diferente (preto, azul, creme, laranja, preto, azul) e corte seco entre eles. Na troca, só uma faixa lima fina passa no topo e no rodapé por 0,3 s. Frases de agência ("IDEIAS GANHAM", "DESIGN QUE PENSA.", "MENOS ATRITO. MAIS FLUXO.", "VAMOS CRIAR JUNTOS?") e assinatura "GPT / ESTÚDIO DE INTELIGÊNCIA". **Diz que é mestre do design, mas não demonstra nada.** Defeitos: o título bate no objeto em 4 das 6 cenas (o quadrado azul come o "M" de GANHAM, o laranja come o "NSA" de PENSA, as barras cortam o "FLUXO", o polígono cruza "JUNTOS?"); as cenas 2, 3 e 4 já entram montadas e passam 4 s só girando devagar (a 4 fica parada); só a 1, a 5 e a 6 têm entrada; paleta de 5 cores berrantes (azul royal + lima + laranja); cabeçalho em 20 px. Tipo grande e legível, sem tela vazia. Sem refino | `<pasta-do-projeto>\bancada-motion\04-terra\terra\` |

Antecedente, com outro teste (o brief guiado da POC-02, juiz às cegas):
- controle v3 7,7;
- Opus 7,0;
- Argon 6,0;
- DeepSeek V4.1 Flash 5,5.

Detalhe em `<pasta-do-projeto>\POC-02-modelos\RESULTADO.md` (não incluído).
