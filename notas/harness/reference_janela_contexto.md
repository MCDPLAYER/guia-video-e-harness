---
name: reference_janela_contexto
description: "quando recall: ate onde posso encher a janela de contexto sem perder qualidade? Por que o Claude esquece minhas regras no meio da sessao? Janela de 1M vale a pena? Qual modelo aguenta contexto longo? Onde fica o limite seguro? Por que a peca sai errada com o contexto cheio? Devo ligar o [1m]? Posso subir o autocompact pra 350k? Teste de agulha no palheiro (NIAH) prova que ate X tokens ta suave?"
metadata: 
  node_type: memory
  type: reference
  modified: 2026-09-23T12:01:06.943Z
---

**Janela anunciada ≠ janela útil, e a diferença é de ordem de grandeza.** Llama 4 Scout anuncia 10M e tem janela efetiva medida de ~1.000 tokens (NoLiMa, ICML 2025). A própria doc da Anthropic admite: *"As token count grows, accuracy and recall degrade, a phenomenon known as context rot."*

🔴 **O que quebra PRIMEIRO não é o conteúdo, é a REGRA** — e quebra em silêncio:

| Tarefa | Começa a quebrar |
|---|---|
| Achar 1 fato literal | ~1M (mas 32K se exigir inferência) |
| **Seguir regra dada antes** | **~4.000 tokens** · −16 a −28 pts de 16K→32K |
| Manter estilo/voz | por **turnos**: >30% após 8–12 |
| Raciocínio multi-passo | **0,92 → 0,68 aos 3.000 tokens** |

Para peça de cliente isso significa: **a regra do `DECISOES.md` some antes do material sumir**, e a peça sai limpa e errada. Não gera erro visível. Mesmo modelo e mesmo comprimento: 97% em múltipla escolha vs **17,4% em sumarização** — não é o volume, é o que se pede.

🚫 **Teste de agulha (NIAH) não é régua pra você:** mede a 1ª linha da tabela, a ÚLTIMA a quebrar. Resposta do tipo *"até 350k tá suave, 95–97%"* vem desse teste. Com o Opus 5.5 nem isso existia: a IA do Google inventou as "zonas" e pendurou citação (checado 23/09, §12 do doc). **250k também não é número mágico:** é rede de segurança (a sessão típica chega a 276k), não o ponto onde a qualidade despenca. Precisa de mais numa sessão → `/autocompact 350k` só nela.

⚠️ **Compactar não é neutro:** agente com restrição em contexto = 0% de violação; **depois da compactação, 30–59%**. Por isso decisão de cliente vai pro `DECISOES.md` dele, nunca confiada ao resumo.

✅ **Grátis e medido: pergunta/tarefa no FIM do prompt** (documento longo em cima, instrução embaixo) = **até +30%** de qualidade, oficial Anthropic.

📐 **Modelo por papel** (medido, ver [[feedback_modelo_tier_policy]]): leitura pesada → **Opus 4.8** (único estável 25K→50K) · tarefa curta → Sonnet 5/Haiku · ⛔ **Sonnet 5 nunca em varredura longa** (−12 pts em 50K) · acima de ~800K o modelo não perde só precisão, **perde a capacidade de notar** (2× a 30× mais falhas).

🔑 **O controle certo é `autoCompactWindow`** — **CONFIRMADO FUNCIONANDO 22/09/2026** (painel passou a mostrar `250k` no lugar de `1M`). ⚠️ **Dois detalhes que custaram duas tentativas falhas:** (a) o valor é **NÚMERO INTEIRO** — gravei `"250k"` como string e não pegou; o app normalizou pra `250000`; (b) editar o `settings.json` **não aplica em sessão nova** — o arquivo só é lido no boot do app. ✅ **O caminho que funciona na hora é o comando `/autocompact 250k` no chat**, que grava sozinho no formato certo. ⚠️ **Opus 5 e Sonnet 5 são 1M FIXO — não existe variante 200K nem sufixo `[1m]` pra tirar** (testado 22/09: removi o `[1m]` e a janela seguiu 1M). Default oficial de quem tem 1M nativo: compacta em **~967k**. Alternativas: `/autocompact 250k` (sessão+futuras) · `--autocompact` (só aquele launch) · `CLAUDE_CODE_AUTO_COMPACT_WINDOW` (env, maior precedência) · `CLAUDE_CODE_DISABLE_1M_CONTEXT=1` (força 200K). ⛔ **`CLAUDE_AUTOCOMPACT_PCT_OVERRIDE` existe mas está quebrado** — várias issues abertas, e em app de GUI não pega.

💰 **O freio não pode ser o custo:** a Anthropic não cobra premium por contexto grande. O freio é a qualidade.

📄 **Dados completos, tabelas por faixa, fontes e datas:** `<pasta-do-drive>/obsidian/_ferramentas/JANELA-DE-CONTEXTO-MODELOS-2026-09.md` (doc não incluído) — ⏳ reconferir a partir de dez/2026, benchmark envelhece rápido.

## Pegadinha + como conferir

**Gotchas que custaram tentativa (22/09/2026):**
- `autoCompactWindow` tem que ser **número inteiro** (`250000`). Gravei `"250k"` como texto e não pegou.
- Editar o `settings.json` **não vale em sessão nova** — o arquivo só é lido no boot do app. Pra valer na hora, use o comando.
- ⛔ `CLAUDE_AUTOCOMPACT_PCT_OVERRIDE` é oficial mas está **quebrado** (issues abertas; não pega em app de interface gráfica). Não perca tempo com ela.
- Opus 5 / Sonnet 5 / Fable = **1M fixo**. Não existe variante 200K nem `[1m]` pra tirar. Só Opus 4.6 e Sonnet 4.6 têm 200K por padrão. **Opus 5.5 (daily driver desde 22/09) = 1M também**, igual ao Opus 5 (conferido 23/09 no artigo da Artificial Analysis). O teto de 250k é configuração do Claude Code, não do modelo, e o painel tem que continuar mostrando `/250k`.

**Verificação concreta — rode e espere isto:**
1. `/autocompact 250k` no chat → responde *"Auto-compact window set to 250k tokens"*, e o painel passa a mostrar `/250k` no lugar de `/1M`.
2. `/context` a qualquer momento → mostra o uso atual e o breakdown por categoria.
3. Pra medir se a mudança pegou ao longo do tempo:
   `python "<pasta-do-drive>/obsidian/_ferramentas/medir-contexto-sessoes.py"` (script do autor, não incluído)
   **Linha de base de 22/09/2026, antes de capar:** 279 sessões, mediana do pico **276.485**, p90 **637.641**, e só **6 compactações (2%)**. Se a mediana caiu numa medição futura, o freio funcionou.
