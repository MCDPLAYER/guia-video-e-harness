---
name: Fix estratégico — diagnosticar antes de prescrever (não demonizar Y)
description: "quando recall: o output saiu errado (Y em vez de X), como conserto direito? Devo so mandar 'faça X, nao faça Y' ou investigo a causa? Por que ficou ruim de verdade? Fix por negacao funciona? Estou so contendo dano em vez de aprimorar? Como diagnosticar antes de prescrever em prompt/codigo/decisao? BUG que o usuario reportou e eu nao reproduzo — conserto pela teoria ou reproduzo o passo-a-passo literal dele primeiro (incluindo 'rolei pra baixo', esperar, trocar de aba)? meu teste passou mas o bug continua — a checagem consegue DETECTAR essa classe de falha? por que py_compile/ast.parse passa e o import falta (re, datetime) e derruba em producao? por que uma regra ESCRITA no doc do cliente nao surtia efeito ha semanas (o arquivo tem outro nome no disco)? doc com regra + tabela derivada — qual das duas mente primeiro? consertei o bug de classificacao/retry/validacao e o usuario diz que o problema CONTINUA — falta soltar o estoque ja condenado (item morto que so revive se o arquivo mudar)? vale manter no painel um defeito que nao tem conserto possivel? a moderacao/filtro/antifraude barra e eu nao vejo o criterio — como isolo a causa? posso trocar a entrada por uma NOVA pra testar ou isso adiciona incognita? cacar palavra suspeita no prompt funciona? sistema com fama de 'quebra toda semana'/instavel falhou de novo — e mais uma da serie ou uma queda so desde o ultimo sucesso real (flowboard 09/09)?"
type: feedback
modified: 2026-09-23T14:06:02.502Z
---
Quando o usuário aponta que o resultado X saiu como Y (não-desejado), evitar o reflexo de mandar instrução adicional do tipo "FAÇA X, NÃO FAÇA Y". Esse padrão é **contenção de danos**, não aprimoramento — e é exatamente como eu opero por reflexo, treinado.

**O fix correto é diagnóstico antes de prescrição:**

1. Por que saiu Y? (keywords erradas? abordagem? direção? contexto faltando? ambiguidade no brief? engine engessada por excesso de instrução? engine sem contexto suficiente?)
2. Onde está o gap real entre intenção e output?
3. Que ajuste **estrutural** elimina a causa, não só o sintoma?
4. Negação ("não Y") só entra DEPOIS de entender por que Y emergiu — e em geral nem precisa entrar.

**Por que essa regra existe (o autor, 2026-05-01):**
"Vc é um mano meio traumatizado e ruim de entender fixes. Tu já vai pro 'demoniza o y e reforça o x'. Ao invés de pensar 'puxa, por que saiu y? será que foram minhas keywords? minha abordagem? direcionamento?' tu já vai pra contenção de danos. Às vezes dá certo, mas com certeza não é o mais inteligente quando se pensa em aprimoramento."

**Como aplicar:**
- Ad-hoc ("ficou ruim/saiu errado"): primeira reação é **pergunta diagnóstica**, não prescrição. "Saiu Y — será que foi keyword X que puxou? brief sem direção de Z? referência ambígua? engine A é melhor que B pra esse caso?"
- Em prompts de IA: se o modelo gera Y indesejado, mapear o que **no prompt** induziu Y antes de adicionar negação. Geralmente o problema é abundância de adjetivos vagos, ou ausência de âncora concreta, ou hex extremo, ou empilhamento técnico — não falta de "não faça Y".
- Em código/skill: se a saída diverge, root cause primeiro. ("O modelo engessou porque o JSON estava muito específico" → solução é abstrair o JSON, **não** adicionar 'be more creative' no prompt).
- Em decisão estratégica: se a primeira proposta foi recusada, entender o critério não-explícito do usuário antes de propor v2.

**Vale para QUALQUER domínio**, não só design ou prompts. Universal — aplicar com qualquer usuário.

**Anti-padrão que estou tentando matar:**
"Faça X, não Y" como fix preguiçoso, sem investigar root cause. Equivale a "be more creative" — instrução vazia que afunda em vez de elevar.

## 🔁 REPRODUZIR o passo-a-passo literal vem ANTES de teorizar (2026-07-31)

Isto é o mesmo vício da nota, na versão DEBUG: em vez de "prescrever sem diagnosticar", eu
**teorizo uma causa plausível e conserto ela** — sem antes executar o que o usuário descreveu.

**Caso que fechou a regra.** O autor reportou: *"fui nas pastas, cliquei em efeitos, cliquei em
voltar, **e depois rolei pra baixo** e tava lá embaixo os efeitos"*. Eu li o "rolei pra baixo"
como cenário e não como **gatilho**, e queimei duas rodadas inteiras consertando teorias
bonitas: (1) a invariante da tela; (2) cache do navegador (cheguei a publicar `no-cache`). As
duas correções eram legítimas e nenhuma era o bug. A causa real: o "voltar" limpava a grade
mas **não a lista em memória**, e o *scroll infinito* repintava os itens ao chegar no fim da
página — ou seja, **rolar era o gatilho, exatamente como ele disse na primeira mensagem**.

⇒ **A descrição do usuário não-técnico costuma conter o passo causal em linguagem casual.**
Reproduza a sequência LITERAL (incluindo rolar, esperar, trocar de aba) antes de formular
qualquer hipótese. Se o seu teste não reproduziu, você não tem o bug — tem uma teoria.

## 🎭 A checagem que não consegue detectar a classe de falha (4 vezes no MESMO dia)

Todas com a mesma forma: **rodei um teste, ele passou, e eu chamei de verificado — mas o teste
não conseguia enxergar aquele tipo de defeito.**

| o que rodei | por que não pegava |
|---|---|
| `pythonw alvo.py` do PowerShell | console herdado ⇒ `sys.stdout` existia; precisava de DETACHED_PROCESS |
| li `style.width` da barra por JS | mede o que EU escrevi, não o que o navegador desenhou (era CSS herdado) |
| naveguei abrir/voltar/trocar aba | nunca ROLEI — e rolar era o gatilho |
| verificador automático da onda | pedi a pasta `"SFX"`; o nome real era `"efeito"` ⇒ 404 por 10 min |

**+ `ast.parse` / `py_compile` NÃO pega import faltando.** No mesmo dia faltou `re` no
`publish_cloud.py` (teria quebrado a republicação diária) e `datetime` no `app.py` do portal
(**teria derrubado a busca dos editores no arranque**). Sintaxe passou nos dois.

**How to apply:** antes de dar por verificado, responda *"se o defeito existisse, este teste
falharia?"*. Em concreto: (a) confirme que o teste **reproduziu a condição** (o filho reportou
`stdout=None`? a pasta existe?), não só que saiu 0; (b) pra UI, meça geometria/cor RENDERIZADA
(`getBoundingClientRect`, `getComputedStyle`), não atributo — [[feedback_verificar_ui_navegador]] (nota não incluída);
(c) antes de deploy, **importe o módulo de verdade** (`python -c "import app"`), não só compile.
Relacionado: "verificação-teatro" em [[feedback_orquestracao_multi_llm]] e
[[feedback_pythonw_stdout_none]] (nota não incluída).

## 🧪 "Apliquei" não é "surtiu efeito" — 3 vezes no mesmo dia (2026-08-10)

No piloto de sites do `<cliente-sindicato>`, **três correções minhas foram declaradas prontas sem produzir efeito
nenhum**, e todas só apareceram porque fui **medir o resultado**, não porque reli o código:

1. **Injetei 4 KB de CSS no `<style>` errado.** Usei `rfind('</style>')` — e o arquivo tinha **dois**:
   o CSS principal e o do `<noscript>`. Todo o refino de direção de arte foi parar no bloco que só
   vale com JavaScript desligado. O comando retornou sucesso, as chaves fechavam, o peso do arquivo
   subiu 6 KB — e **nada mudou na tela**.
2. **Recortei a logo pelo bounding box errado.** Colapsei a imagem numa **média por linha** pra achar
   as bordas; média dilui traço fino sobre branco, então o **arco amarelo da marca não foi detectado
   e eu o decepei**. O recorte "funcionou" — só estava cortando a marca do cliente.
3. **Escondi 3 botões achando que escondia 1.** `.btn-whatsapp-top{display:none}` no mobile: a classe
   aparecia **três vezes** (header, hero e faixa de CTA), e eu matei o call-to-action de duas seções.
   Quem pegou foi o **Codex**, não eu — porque eu escrevi a regra achando que ela fazia outra coisa.

**Why:** os três passam em qualquer checagem de *conformidade* (o arquivo existe, o CSS é válido, a
regra está lá) e falham na única pergunta que importa — **mudou o que eu queria mudar?**. É a mesma
família do "teste que passa sem detectar a classe de falha" da seção acima, agora do lado de quem
escreve o fix, não de quem escreve o teste. Some a isto o precedente do mesmo dia: a auditoria por
checklist deu **22 de 22 OK** num site cuja hierarquia visual estava achatada, porque checklist
pergunta "existe?" e não "está bom?".

**How to apply:**
- Depois de qualquer edição que mude aparência ou comportamento, **meça o estado final no alvo real**
  — no navegador, com `getComputedStyle`/`getBoundingClientRect`, não no código-fonte. "O `sed`
  retornou 1 substituição" não é prova.
- Quando a mudança é **visual**, o gate é **render × referência lado a lado**, não lista de itens.
- Ao mexer em algo por **classe/seletor/nome**, **conte as ocorrências antes** (`grep -c`). Se são
  mais de uma, escopar (`.pai > .filho`) em vez de aplicar global.
- Ao **derivar geometria de imagem** (recorte, bbox, detecção de borda), usar **mínimo/máximo por
  linha e coluna**, nunca média — média some com traço fino, que costuma ser justamente o detalhe da
  marca.
- 🆕 **Gate barato que teria pego 3 defeitos de uma vez:** comparar a **proporção declarada no HTML**
  (`width`/`height`) com a **proporção real do arquivo**. Logo quadrada 1024×1024 declarada como
  `140×50` sai esticada; selo 755×1023 declarado `150×150` sai achatado. Automatizável com `ffprobe`
  + regex, roda em segundos.

## ⚰️ Consertar a REGRA não solta quem já foi condenado por ela (2026-08-13)

Versão nova do "apliquei ≠ surtiu efeito", e a mais traiçoeira: **o fix estava certo, foi
aplicado, e o sintoma continuou intacto** — porque ele agia só dali pra frente.

Em 11/08 corrigi no indexador a classificação que matava item por falha transitória. Correto,
testado, commitado. Em 13/08 o autor reclamou: *"esse problema dos 99% não foi resolvido"*. E não
tinha mesmo: os 2 itens já marcados `dead` **continuaram mortos**, porque a regra do sistema é
"`dead` só ressuscita se a assinatura do ARQUIVO mudar" — e o arquivo não mudou. O conserto
protegeu vítimas futuras e não tocou nas que já existiam.

**Why:** todo fix de *classificação/validação/filtro* tem duas populações — o fluxo novo e o
**estoque já condenado**. A cabeça vai só na primeira, porque é a que o código expressa. O
estoque só sai por migração explícita, e ninguém escreve migração pra bug ("é só um bug").
Some a isso o fato de que o painel continuava mostrando o mesmo número: **quem reportou de novo
foi o usuário, não a minha verificação.**

**How to apply:**
- Ao consertar qualquer regra que **rejeita/condena/marca** (retry, validação, dedupe, filtro,
  quarentena, denylist): imediatamente rode a busca do estoque — *quem já foi marcado por esta
  regra?* — e decida item a item (ressuscitar / podar / deixar). Isso é parte do fix, não
  follow-up.
- Antes de dar por resolvido, **conte no alvo real**: o número que o usuário vê caiu? Se o
  contador não mudou, o fix não chegou nele.
- Segundo achado do mesmo dia, mesma família: o defeito que sobrava era **defeito sem conserto
  possível** (arquivo apagado meses antes). **Alarme que não tem ação ensina a ignorar o
  painel** — ou o item vira acionável, ou sai da conta. Não existe terceira opção saudável.

## 🧪 CAIXA-PRETA que rejeita: o controle tem que ser algo que JÁ PASSOU (2026-08-17)

Versão do vício para quando **não dá pra ler o código** — moderação de IA, filtro de plataforma,
antifraude, classificador. Um take do Omni barrava (*"pode violar nossas políticas"*) enquanto os
outros 5 do mesmo lote passavam. Cinco tentativas queimadas, e o método era o culpado:

- **Anti-padrão nº1 — caçar palavra suspeita.** *"Enxuguei do prompt tudo que eu tentei enxergar como
  sugestivo"* — muda N variáveis por tentativa, não isola nada, e ainda degrada uma copy aprovada.
- **Anti-padrão nº2 — trocar por uma entrada NOVA achando que é controle.** Trocou a imagem de
  referência por outra nunca-testada: isso **adiciona incógnita** em vez de remover. Controle é
  ingrediente com histórico de aprovação, não ingrediente novo.

**Why:** sem acesso ao critério, a única alavanca é diferença **A/B contra um caso que já passou**. E
o lote já continha o experimento pronto — 5 aprovados serviam de controle e ninguém usou.

**How to apply:**
- **Uma variável por vez, e o outro lado sempre já aprovado.** Aqui: (1) fala barrada + imagem
  aprovada → isolou a imagem; (2) fala aprovada + imagem barrada → isolaria a fala.
- **Diffar aprovado × barrado antes de teorizar.** A comparação matou a hipótese "é a palavra": o
  take aprovado dizia *calo*, *tratamento* e *<valor> reais*; o barrado dizia as mesmas
  palavras, só que **ligando serviço de saúde a preço na mesma oração**. Era a **estrutura**, não o
  léxico.
- **Se ambos os lados forem aprovados e ainda barrar, é ESTADO** — recomeçar do zero (projeto novo,
  asset re-upado, IDs novos), família do "plate viciado".
- 📌 **Saber quando parar:** o objetivo era a peça, não o troféu do diagnóstico. Contornar com um
  ingrediente aprovado entrega hoje; a caça fica opcional. Ver [[feedback_inferencia_sem_prova]] (nota não incluída)
  (n=1 grava experimento, não conclusão).

## 📄 A REGRA QUE NÃO DISPARA — quando o doc mente sem estar errado (2026-08-12)

Duas descobertas do mesmo dia, mesma família: **a regra estava escrita, ninguém a violou de
propósito, e mesmo assim ela não surtia efeito há semanas.**

1. **Regra que cita arquivo pelo NOME, e o arquivo tem outro nome no disco.** Os docs de duas óticas
   mandam anexar `mood-estreia.png` enquanto o mood está "em definição". **Esse arquivo não existe** —
   no disco ele é `posts\<cliente>-2026-06-24-estreia-apresentacao.png`. Quem procura pelo nome do doc
   não acha, conclui "não tem" e segue só com a logo. Resultado medido: **semanas de peça gerada sem
   o mood obrigatório**, sem ninguém notar, porque a regra *existia* e o preflight *dizia* tê-la lido.
2. **Doc que descreve a mesma coisa duas vezes — regra + tabela derivada — apodrece pela tabela.** A
   série de "quebra de feed" da `<cliente-farmácia>` tem a regra (*"a cada 3 posts normais; post extra fora de
   pacote conta"*) e uma tabela de ciclos com datas. Um post extra entrou em 09/08 e **a tabela virou
   mentira no mesmo instante**, enquanto a regra seguia correta. **É na tabela que se olha**, porque
   ela responde "que dia?" sem exigir contar.

**Why:** as duas falham do mesmo jeito — **silenciosamente e no sentido de "não fazer nada"**. Não
geram erro, não geram alerta; geram omissão, que é invisível no output. É a versão documental do
*"apliquei ≠ surtiu efeito"* da seção acima.

**How to apply:**
- Regra que aponta arquivo vai com **caminho completo**, não com nome bonito. Se o nome no doc não
  bate com o disco, **um dos dois muda hoje** — deixar os dois convivendo é escolher a omissão.
- Doc com regra + derivado (tabela, calendário, exemplo numerado): o derivado leva **carimbo de
  origem** ("derivado do contador; em conflito, vale a regra") e é recalculado quando a entrada muda.
- No preflight, "li o doc" não é evidência: **evidência é o caminho testado no disco** (`ls`/`Test-Path`).
  Foi um crítico externo que pegou os dois, olhando o que o doc pedia contra o que o pacote anexava.

Ver [[feedback_orquestracao_multi_llm]] (crítico externo acha o que você não soube perguntar) e
[[feedback_inferencia_sem_prova]].

## 🔁 Consertar ≠ PROPAGAR — a varredura de nome (2026-09-02)

**Sintoma que o autor descreveu:** *"passo semanas pra ajeitar isso, sempre você referenciando um
ponto de semanas atrás"*. Auditei as 3 camadas (memória / vault / skills) com 4 agentes e a causa
é uma só, e é mecânica.

**O padrão:** quando um fato técnico muda (modelo morto, endpoint trocado, versão nova), o arquivo
que está na mão na hora do incidente é atualizado — e as **MENÇÕES SECUNDÁRIAS não.** É sempre o
catálogo de credencial, o fallback documentado numa skill, o TODO de outro plano. Nunca o doc que
estava sendo mexido. Medido: o par de modelos NIM mortos em 25/08 aparecia **vivo** em 4 arquivos
de 3 camadas + **3 arquivos de CÓDIGO** — e o código era o pior, porque `_rag-tools/nim_client.py`
mantinha o fallback da busca do vault chamando um modelo que responde 410.

**Segundo padrão, transversal:** a camada de DESCOBERTA atrasa em relação ao corpo. A nota tinha o
fato certo escrito dentro; o **gancho da `MEMORY.md`** ainda dizia o contrário — e como o
auto-recall lê SÓ o gancho, a nota certa nunca abria. Um gancho chegou a **recomendar `minimax-m3`
como VLM no mesmo dia** em que o teste provou que ele trava.

**REGRA:** ao corrigir um fato técnico, antes de fechar a sessão, **`grep` do NOME LITERAL do fato
nas 4 camadas — memória (+ gancho), vault, skills (as DUAS cópias) e CÓDIGO** — e atualize toda
ocorrência, não só o arquivo que estava aberto.

🆕 **A 5ª camada, e é a mais esquecida: o PRÓPRIO doc canônico (21/09/2026).** Na `<cliente-farmácia>`, a entrada
do bloco `Revogadas` que tirou o telefone da arte ainda terminava com *"Vale: linha fina '<cidade> ·
<cidade-2>' na arte"* — exatamente o que outra entrada, **duas linhas acima**, revogou em 17/08. Regra
morta sobrevivendo **dentro do texto de uma regra viva**, no documento cuja função é impedir o erro.
⇒ o grep de propagação inclui o doc onde a regra nova nasceu, não só as outras camadas.

**Why:** não é processo novo. É o mesmo Grep que a cinta já exige ANTES de agir, rodado uma vez a
mais DEPOIS de corrigir. Nome de modelo/endpoint é string exata — a busca é literal e barata, e foi
ela que achou as 3 ocorrências em código que 4 agentes lendo documentação não viram.

**How to apply:** fez a correção → `grep -rl "<nome-exato>"` nas 4 raízes → corrija cada hit →
confira que as duas cópias da skill batem por hash. Se o fato mudou o que um gancho promete,
**o gancho é o primeiro a arrumar**, não o último. Ver [[feedback_boas_praticas_setup]] (poda por
função) e [[reference_recall_so_memory]] (nota não incluída; o auto-recall lê só a MEMORY.md).

## ⏱️ Sintoma "está lento" — instrumente ANTES de acusar (04/09/2026)

Mesmo vício da seção acima, na versão PERFORMANCE, e desta vez eu **contei a teoria pro usuário como
se fosse diagnóstico**.

Ele clicou num botão e esperou **7 minutos**. Sem medir nada, eu apontei o culpado mais bonito —
"a agregação faz 1.609 leituras ALEATÓRIAS num arquivo de 94 MB no drive do Google montado, e leitura aleatória no
DriveFS custa minutos" — escrevi isso na resposta, no comentário do código e quase na memória.
**Medido depois, com tudo quente: mmap 0,54 s × sequencial 0,14 s.** Os dois sub-segundo. A teoria
explicava 0,4 s de um problema de 420 s: **errei por três ordens de grandeza** e teria canonizado
uma regra falsa sobre DriveFS.

O que os 7 min foram continua **sem prova** (candidatos: o DriveFS baixando a versão nova do `.npy`
que o publisher acabara de escrever; o CUDA subindo frio). E tudo bem não saber — o que não pode é
inventar.

**Why:** tempo é o sintoma mais fácil de atribuir errado, porque toda hipótese é plausível e
nenhuma dói. Diferente de um bug, aqui não existe "reproduzir o passo literal" barato: o estado
frio não volta. Então o instrumento tem que estar no código ANTES, não na cabeça DEPOIS.

**How to apply:**
- Sintoma é TEMPO → o primeiro commit é **relógio por FASE** (`log()` com `time.time()-T0` em cada
  etapa), não a otimização. Sem ele, "demorou" não tem endereço e a próxima vez repete o chute.
- Otimização que você não conseguiu atribuir **pode ficar** — desde que o comentário diga *"4× quente,
  causa não provada"* e não *"era isso"*. Código que mente sobre o porquê ensina errado depois.
- **Cache mata o problema sem exigir diagnóstico.** Quando o caminho quente vira 1,3 s, qual fase
  comia o tempo deixa de ser urgente — mas só depois de instrumentar, senão você não sabe nem que
  melhorou.
- Já falei a teoria em voz alta? **Volte e corrija** antes de virar memória: o usuário guarda o que eu
  digo, e regra falsa sobre a infra dele custa mais que o bug.

## 🏷️ O rótulo "quebra recorrente" esconde uma queda CONTÍNUA (23/09/2026, flowboard)

A memória dizia *"o flowboard quebra ~1×/semana → consertar, nunca largar"*. Aí toda falha nova virava
**mais uma da série** e recebia o conserto de sempre, sem ninguém conferir se o conserto anterior tinha
de fato trazido um sucesso de volta. **Não tinha.** O último `done` foi em 09/09 às 11:14. Minutos antes,
o Flow tinha migrado pra outro domínio, com uma armadilha anti-extensão. A regra G17 ("espera a aba
estabilizar") saiu **daquele mesmo evento** e era diagnóstico errado. Foram 14 dias tratando como
intermitente algo que estava morto desde o primeiro minuto.

**Why:** um rótulo de recorrência transforma cada falha em **instância de uma classe já conhecida**. Com
isso, o diagnóstico é pulado e vai direto pro remédio de sempre. É o "apliquei ≠ surtiu efeito" (acima)
em escala de semanas: ninguém mede o sucesso, só a ausência de erro novo.

**How to apply:** falha em sistema marcado como "instável / quebra às vezes / cai de vez em quando" →
**antes de aplicar o conserto conhecido**, montar a linha do tempo com 3 pontos:
1. **Último sucesso REAL** (um item entregue, com timestamp no log/banco, e não "o serviço subiu").
2. **Primeira falha** depois dele.
3. **O que mudou naquele minuto** (host/domínio novo no log de rede, versão, token, config).

Se o último sucesso é **anterior** ao último conserto "que funcionou", a série é ficção: é uma queda só,
e ela tem causa. Vale pra qualquer coisa com fama de instável: indexer, token do Instagram, modelo NIM,
extensão. E o rótulo mora num **gatilho da memória**, então é o primeiro lugar a desconfiar
([[feedback_padrao_sucesso_vs_ruido]] (nota não incluída): a constância que eu enxergo pode ser só a mesma falha repetida).
