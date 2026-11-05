# Aprendizados de vídeo + harness (Claude Code)

Este repositório é o setup de Claude Code de um editor de vídeo / criador de conteúdo brasileiro ("o autor"),
limpo para outra pessoa ler e reaproveitar no próprio setup. Tudo em PT-BR.

São skills, regras do harness (`CLAUDE.md`, subagentes) e notas de memória que explicam o **porquê** de cada
regra, com o número medido e a data do teste. Clientes, nomes, caminhos e números do canal foram trocados por
placeholders ou tirados.

## Por onde começar

1. **`GUIA-VIDEO.md`**: as lições de vídeo e imagem num lugar só (motion por código, Google Flow/Omni, imagem
   estática, áudio/transcrição, roteiro, analytics, limpeza de disco), cada uma apontando o arquivo detalhado.
2. **`GUIA-HARNESS.md`**: as lições de setup (CLAUDE.md enxuto, poda de regras, subagentes e custo,
   orquestração com Codex/Gemini, crítico às cegas, debug, contexto, captura de aprendizado).
3. Depois, abra só a skill ou nota do assunto que interessa. Ninguém precisa ler tudo.

## O que tem em cada pasta

| Pasta | O que é | Use para |
|---|---|---|
| `harness/CLAUDE.md` | O `CLAUDE.md` global do autor, anonimizado | Modelo de **estrutura**: cinta curta → rotas IF→THEN → princípios. As rotas são dele; troque pelas suas |
| `harness/agents/` | 4 subagentes por papel: `scout`, `worker`, `smart-worker`, `pesquisador` | Copiar para `~/.claude/agents/` e ajustar modelo/escopo |
| `notas/harness/` | Memórias sobre o harness (custo, crítico, multi-LLM, poda, hooks, janela de contexto, debug) | Entender o porquê das regras do guia |
| `notas/video/` | Memórias técnicas de vídeo (ffmpeg, transcrição, TTS, bancada de modelos, julgar vídeo gerado) | Receitas e armadilhas medidas |
| `skills/motion-studio/` | Motion design por código (HTML/canvas renderizado quadro a quadro), com estilos e referências | Ver `SKILL.md` e depois o `estilos/*.md` do estilo |
| `skills/gflow/` | Mapa do Google Flow / Gemini Omni: preço, prompts, moderação, operação pela UI | `SKILL.md` + `ref/changelog.md` (achados datados) |
| `skills/peca/` | Peça estática de cliente (card, carrossel) com método curto: carta + ficha de fatos + revisor às cegas | `ref/REVISOR.md` e `ref/CARTA-MODELO.md` são os mais reaproveitáveis |
| `skills/roteiro/` | Roteiro de vídeo-ensaio | **Moldada para o canal do autor**: reaproveite o método |
| `skills/canal-youtube/` | "Segundo cérebro" do canal: título, thumb, pauta, formato curto | Idem: método sim, nicho e tom não |
| `skills/pipeline-multi-llm/` | Como usar Codex CLI e Antigravity (Gemini) como "funcionários" do Claude | `SKILL.md` (muito detalhe de Windows) + `ref/historico-2026.md` |
| `skills/media-tools-local/` | ffmpeg, Whisper, yt-dlp, demucs instalados localmente | Receitas de linha de comando |
| `skills/capturar/`, `skills/digest-mensal/` | Rituais de captura de aprendizado (por sessão e mensal) | Mecanismo reaproveitável; rotina do autor marcada |
| `skills/limpar/` | Liberar espaço em disco sem perder nada (gate por hash, holding, Lixeira) | O método; as pastas são do autor |

Para usar uma skill: copie a pasta para `~/.claude/skills/` e ajuste a `description` e os placeholders.
Vários scripts citados (`.py`, `.ps1`, `.html`) **não vieram** neste export, que só tem `.md`. Os textos
avisam onde ("não incluído").

## Convenções da limpeza

- **"o autor"** = quem escreveu o material. Nas instruções ao Claude aparece como **"o usuário"**.
- **(preferência do autor)** / **"> Preferência do autor"** = gosto, rotina ou cliente dele, não regra
  universal. O que não tem essa marca é aprendizado medido.
- **⏳** = dado perecível (preço, versão, cota, disponibilidade). Os testes são de 2026.
- **`[[nome_de_nota]] (nota não incluída)`** = ponteiro para memória do autor que não veio no export.
- **Placeholders:**
  - clientes: `<cliente-farmácia>`, `<cliente-app>`, `<cliente-ótica>` (e `-2`, `-3`, `-4`),
    `<cliente-sindicato>`, `<cliente-entidade-lojista>`, `<cliente-associação-comercial>`,
    `<cliente-A>` … `<cliente-K>`;
  - vídeos do canal: `<video-ensaio-NN>`;
  - parceria: `<parceria-musical>`;
  - cidade: `<cidade>`;
  - pastas: `<pasta-do-projeto>`, `<pasta-de-midia>`, `<pasta-do-drive>`, `<pasta-do-usuario>`,
    `<pasta-de-skills>`, `<pasta-de-memoria>` e similares.
- **Nomes de estilo** como "Kitey" e "32Bits" (em `motion-studio/estilos/`) são rótulos de estilo que vêm de
  criadores públicos usados como referência estética.
