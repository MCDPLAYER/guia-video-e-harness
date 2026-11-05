---
name: feedback_usar_o_que_ja_existe
description: "Antes de escrever função/componente numa peça, AUDITAR o que o kit já tem — medido 21/09 numa peça de `<cliente-entidade-lojista>`: a peça aprovada usou o kit 13×, a reprovada 1×, e a reprovada reimplementou 6 símbolos (inclusive errado). Vale pra qualquer acervo documentado do autor, não só Remotion."
metadata: 
  node_type: memory
  type: feedback
  modified: 2026-09-21T14:25:25.664Z
---

O autor, 21/09, depois de a peça `<cliente-entidade-lojista>` sair com veredito **TEMPLATE** da lente cega:
*"tanta coisa boa foi aprendida que não vejo sendo usada aqui, o catálogo de presets e
tudo mais, pode ser por isso que o vídeo tá ruim"* — e antes disso: *"porra, então
começou tudo errado"*.

**Ele estava certo, e o número prova.** Auditoria de adoção do kit (`_loop/adocao.sh`):

| peça | kit/motion | kit/verbos | total |
|---|---|---|---|
| **`<cliente-G>`** — a única que o autor aprovou | 7 | 6 | 13 usos |
| **`<cliente-entidade-lojista>`** — a que saiu "template" | **0** | **0** | 1 uso (6% dos módulos) |

A peça `<cliente-entidade-lojista>` reimplementou **6 símbolos que o kit já exportava**: `DUR`, `camera`, `ciclo`,
`deriva`, `pulsa`, `vida`. E reimplementou **errado**: a minha versão de ruído era um
HASH (`frac(sin(x·12.9898)·43758)`), que não tem continuidade — a peça inteira tremeu
por sete renders. `deriva()` e `respira()` do kit faziam exatamente o que eu precisava.

**Why:** a causa raiz não foi "usei a fórmula errada", foi **inventei em vez de abrir**.
E não foi por falta de instrução: a skill `video-remotion` já mandava ler o `PRESETS.md`
antes de inventar componente, e já prescrevia *"todo import termina com auditoria de
ADOÇÃO, não de compilação; zero usos = decisão pendente, não sucesso"*. Eu li a skill no
começo da sessão e mesmo assim não abri os arquivos que ela aponta. **Instrução em prosa
não se cumpre sozinha; número na cara do loop, sim.**

**How to apply:** antes de escrever componente ou função numa peça, rode a auditoria —
ela é barata e roda sem LLM. No Remotion é `bash _loop/adocao.sh src/<peca>`, já plugado
como passo 0 do `_loop/<peca>/ciclo.sh` (antes das réguas, antes das lentes). Ela imprime
adoção por módulo E símbolos reimplementados. Quando for delegar, **o brief carrega a
lista de componentes com assinatura** — mandar "leia o catálogo" não basta, o executor
também pula.

⚠️ **Não é proibição de escrever do zero.** Peça nova pode e deve ter cara nova — a
própria skill diz isso. O que a auditoria garante é que "escrevi do zero" seja uma
DECISÃO registrada, não um descuido. Adoção zero sem justificativa no relatório é
decisão pendente.

---

## 🔴 A REINCIDÊNCIA, no MESMO dia (21/09) — e é a versão que importa

Escrevi a nota acima de manhã, achando que o problema era o KIT DE CÓDIGO. Horas depois
o autor voltou, mais bravo: *"MERMAO COMO ASSIM 1 LENTE DE ARTE E NENHUM SONHADOR? onde
tá tudo que aprendemos nas últimas peças? to vendo um monte de coisa passando que já
foi aprendida."*

Ele estava certo e o buraco era maior. **O MÉTODO também está documentado** — em
`METODO-LOOP.md` (6,5 KB), `LOOP-CEGO.md`, `PONTOS-CEGOS.md` (30 KB), `kit/DIRECAO.md`
— e eu tinha lido só a SKILL, que é o índice. Cheguei a **citar `kit/DIRECAO.md` num
brief de executor sem nunca ter aberto o arquivo**.

O `METODO-LOOP.md` descreve o defeito desta sessão antes de ele acontecer:

> *"as notas subiam em ritmo/acabamento e **empacavam em originalidade**, porque nem o
> executor nem o crítico inventam nada"*

Foi exatamente isso: a lente cega foi de 55% → **90% de acerto** e o autor olhou e achou
FEIO. Compreensão subiu, beleza empacou. E mais três coisas que estavam escritas lá:

| o doc dizia | o que eu fiz |
|---|---|
| "duas lentes independentes > uma lente boa" | rodei **uma** (cega 5×, arte 1×, sonhador 0×) |
| ordem de ataque: bug → sincronia → tela morta → **ACABAMENTO** → som → re-imaginar | parei no 3. Os 8 pontos da bronca dele são todos o **4** |
| "crítica com vídeo+som: 140-190k tokens, **na quota do Google**" | passei a sessão **racionando lente por medo de cota**, com o custo já medido no doc |

🔑 **Réguas medem AUSÊNCIA DE DEFEITO; acabamento e beleza são PRESENÇA DE QUALIDADE.**
Nenhuma régua acha "feio". Quem acha é a lente de ARTE e o SONHADOR — e o sonhador é
descrito no doc como *"a mudança que mais rendeu"*. Uma peça pode passar em todas as
métricas e ser feia; foi o que aconteceu.

🔬 **Prova de que o instrumento funciona**: rodado o sonhador num pedaço de 11s, ele
achou sozinho as três primeiras críticas do autor, sem vê-las — inclusive a solução
("a loja tem que nascer no ápice da beleza, senão o esmagamento do ato seguinte não dói").

**How to apply (versão larga):** `bash _loop/processo.sh <peca>` é passo **−1** do ciclo,
antes da adoção e das réguas. Ele imprime quantas vezes cada lente rodou, lista os
documentos do método com tamanho, mostra a ordem de ataque e lembra que lente é barata.
E prova de lente é o **arquivo de RESPOSTA**, nunca o log: dei uma lente de arte por
morta olhando um log de 0 byte enquanto havia **19 KB de resposta no disco**, que ficou
4 horas sem ser aberta — e ela já apontava o defeito que só descobri muito depois.

🪤 **O padrão em uma frase: eu leio o mapa e não vou aos lugares.** Vale fora do
Remotion — mesma forma do preflight de cliente, que lê o índice e não o `DECISOES.md`.

---

🔁 O gêmeo disso em medição está em [[feedback_fix_root_cause]] ("apliquei" ≠ "surtiu
efeito"); o mesmo vício de pular o que já existe aparece em [[feedback_brand_profile_e_espelho]] (nota não incluída)
(ler o `DECISOES.md` do cliente antes de criar sistema visual). O caso completo com os
números vive na skill `video-remotion`.
