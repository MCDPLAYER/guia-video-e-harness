# Modelo de CARTA de cliente

> Uma página, **teto de 60 linhas e 9 KB** (linha comprida demais é o jeito de furar o teto sem parecer). É a única leitura de cliente na produção de peça.
> Entra só o que cabe numa das seis seções. Gosto e remendo de falha antiga ficam de fora (vão pra SOBRAS).
> Escrita em positivo: diz o que a peça FAZ. "Nunca" só onde a linha é lei, fato ou pedido do cliente.
> Linha nova reescreve a que já fala do assunto. Entrou, saiu.

```markdown
---
cliente: <Nome como o cliente escreve>
atualizado: <AAAA-MM-DD>
teto: 60 linhas
---
# CARTA · <Cliente>

## Quem é
<Até 4 linhas: o que vende, onde, pra quem fala, o que o feed faz por ele.>

## Verdade (lei · fato · dinheiro)
- <O que a peça pode e não pode afirmar por força de lei, conselho ou política de plataforma.> [fonte]
- <Fatos fixos do negócio: o que ele faz e não faz, preço que pode aparecer, prazo.> [fonte]

## Pedido do cliente ou da agência
- <O que ele ou a agência pediu com todas as letras.> (quem, data)

## Marca e assets
- Nome: <grafia exata>
- Logo: <caminho do arquivo> · <como aparece na peça>
- Cor do design: <as cores da marca, pelo nome que o modelo de imagem entende>
- Assinatura gráfica: <só o que identifica a marca de longe: um elemento, uma família de letra>
- Material real: <caminho de produto, foto, tela, embalagem>

## Formato e canal
- Grade: <dia → formato>
- Proporção: <feed 4:5 etc.>
- Onde vai preço, contato e CTA: <arte ou legenda>
- Assinatura fixa da legenda: <texto literal>

## O que já ganhou aqui
- <Mecanismo que venceu, em positivo, com escopo e evidência: peça, data, fala do diretor/autor ou do cliente.>

## O que muda de peça pra peça
- <Os eixos de variação deste cliente. O que a marca fixa fica em "Marca e assets"; o resto se mexe.>

## Onde está o resto
- Vitrine: `_design\VITRINE.jpg` (fontes em `_design\VITRINE.md`)
- Histórico e changelog: `_estrategia\DECISOES.md`
- O que ficou de fora desta carta: `_estrategia\CARTA-SOBRAS.md`
```

## Como uma regra antiga é classificada

| Classe | Entra na carta? | Exemplo |
|---|---|---|
| **LEI/FATO/DINHEIRO** | sim, em "Verdade" | regra de conselho de classe, decreto, política de anúncio, preço, o que o cliente vende |
| **PEDIDO** | sim, em "Pedido" | "a agência pediu QR code na nota", "o dono quer o selo da Caixa no sorteio" |
| **MARCA** | sim, em "Marca e assets" | grafia do nome, arquivo da logo, cores, o elemento que identifica |
| **FORMATO** | sim, em "Formato e canal" | grade, proporção, assinatura da legenda |
| **GANHOU** | sim, em "O que já ganhou" | mecanismo com evidência de aprovação |
| **GOSTO/REMENDO** | não, vai pra SOBRAS | "card de água saiu feio", regra de composição, lista de palavras, checklist |
| **MECÂNICA DE FERRAMENTA** | não, mora em `ref\` da skill | acento no prompt, proporção, como anexar |

Na dúvida entre Verdade e Gosto: vai pra SOBRAS marcada com `?`, e o diretor decide.
