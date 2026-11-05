# Gate de TEXTO e ponteiros — Fase 3 do `/limpar`

Movido do `SKILL.md` (Fase 3). As 4 condições da deleção ficam no `SKILL.md`; aqui está o
detalhe de como provar que **texto** já existe fora do Desktop, e as armadilhas de "nota do RAG
que diz que está salvo".

### ⚠️ Texto não passa por hash contra o disco de mídia — ele vive no Drive
`copy.txt`/`prompt.txt` **sempre** voltam como `UNICO` na triagem, mesmo já estando no RAG (a
triagem varre o disco de mídia, e o texto mora no Drive). Pra texto o teste é **conteúdo CONTIDO no canônico**.

**MEÇA a % de linhas contidas — não olhe e ache, e não confie no precedente.** `Contains()` do
arquivo inteiro dá `False` por qualquer quebra de linha e não te diz *quanto* falta. Compare
**linha a linha** (só as ≥40 chars, espaço normalizado) e **exija 100%**:

```powershell
$linhas = (Get-Content $txt) | %{ ($_ -replace '\s+',' ').Trim() } | ?{ $_.Length -ge 40 }
$can    = (Get-Content $canonico -Raw) -replace '\s+',' '
100 * ($linhas | ?{ $can.Contains($_) }).Count / $linhas.Count    # < 100 = NÃO é dup
```

🔴 **Sobreposição parcial não é redundância — é conteúdo que só existe ali.** Em 03/08 o
`PROMPT-E-LEGENDA.txt` de cada peça deu **~50%** contra o `_pacote-posts` do mesmo dia: o pacote
tem a estratégia e a copy, mas o **checklist "conferir antes de postar"** e as instruções de
anexo/medição só existiam no kit do Desktop. Apagar "porque tá no pacote" teria queimado o
checklist de 17 peças. E note: **em 14/07 esse mesmo par de documentos batia 100%** — o kit
evoluiu e ganhou conteúdo próprio. Par que já foi redundante não segue redundante; meça sempre.

Não fechou 100% → **absorve o delta pro RAG** (uma nota `.md` consolidada por dia/kit, com
frontmatter), **reconfere 100% contra a nota nova**, e só então apaga.

### 🪤 A armadilha: nota do RAG que APONTA pro Desktop
O tracker `2026-07-13-01-<cliente-farmácia>.md` dizia:
> *"Copy + prompt: [[_pacote-posts-2026-07-13]] — kit no Desktop posts-13-07/01-<cliente-farmácia>/"*

**Isso não é backup — é um ponteiro pro arquivo que eu ia apagar.**

➡️ Achou o texto "no RAG"? **Resolve o wikilink e confere o conteúdo lá dentro.**
- O canônico **contém** o texto → dup real → Lixeira. *(foi o caso: o `_pacote-posts` tinha a
  copy+prompt dos 7 clientes na íntegra)*
- O canônico só **aponta** pro Desktop → o Desktop é a **única** cópia → **absorve pro RAG
  primeiro, conserta o ponteiro**, e só então apaga.

🔴 **O ponteiro se disfarça de metadado — olhe o FRONTMATTER, não só o corpo.** Em 03/08 o tracker
`2026-07-30-carrossel-<campanha>.md` trazia:
> `entrega: "<pasta-do-usuario>\Desktop\<campanha>\carrosel\FINAL\ (01..17 + LEGENDA.md)"`

Um campo chamado `entrega:` **lê como prova de que a peça já foi arquivada** — é exatamente o
contrário. As 19 chapas do carrossel só existiam no Desktop (a triagem devolveu **0 DUP** naquela
pasta). ⇒ Antes de apagar, grepe o vault por `Desktop` e trate `entrega:`/`saida:`/`kit:`/`pasta:`
como ponteiro suspeito até provar o contrário. **Uma pasta inteira voltando 0 DUP é o sinal** —
significa que nada dali nunca foi pro disco de mídia, não que "é tudo lixo novo".

🔴 **A forma mais perigosa não aponta nem promete — ela AFIRMA que o backup existe.** Em 12/08 o
`ORDEM-DO-DIA.txt` do próprio dia fechava assim:

> *"Salvar os renders em `<pasta-de-trabalho>\<cliente>\`. Esta pasta pode ir pra Lixeira depois — os
> originais estão todos no `<pasta-de-midia>`."*

Não estavam. **58 artes de 10 clientes, de 04/08 a 12/08, só existiam no Desktop** — o último
arquivamento real tinha sido 03/08, e a frase vinha repetida no template havia 9 dias. Uma linha
que descreve um passo que ninguém executou lê como **permissão pra apagar**.
(O arquivamento das artes no disco de mídia é do `/limpar`; a skill `peca` só entrega no Desktop.)

⇒ **Regra:** frase de doc **nunca** substitui o gate. Quem responde "já está no disco" é o hash, não o
texto — e menos ainda um texto escrito *antes* de o passo rodar.

🔎 **O sinal, visto do outro lado: pasta de DESTINO que existe e está VAZIA.** `<pasta-de-peças>\07-08-<tema>\`,
`<cliente-ótica-3>\posts\` e `09-08-dia-dos-pais\stories\` estavam criadas e vazias — alguém montou o
caminho e nunca copiou. É o mesmo buraco que o "0 DUP" denuncia, e aparece **antes** de você
hashear nada:

```powershell
Get-ChildItem <pasta-de-trabalho> -Recurse -Directory |
  Where-Object { -not (Get-ChildItem $_.FullName -File -Recurse -EA SilentlyContinue) }
```
