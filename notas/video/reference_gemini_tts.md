---
name: reference_gemini_tts
description: "quando recall: preciso de locução/narração/voz IA em PT-BR, qual TTS usar? Gemini 3.8 Flash TTS vs ElevenLabs? como clonar voz no Gemini, por que voices.create dá 500 INTERNAL? cadê a voz clonada (voice_id) e como recriar quando expira? tem voz nativa pt-BR, sotaque regional, voice design? tem voice changer / speech-to-speech / dublagem com timing no Gemini? como chamar a API de TTS se o SDK não tem voices?"
metadata:
  node_type: memory
  type: reference
  modified: 2026-10-06T19:30:34.612Z
---

**Gemini 3.8 Flash TTS** (e o Flash-Lite TTS), lançados em 23/09/2026. Testado ao vivo em 24/09 com 1v1 contra ElevenLabs.
**Ferramenta pronta (do autor, não incluída):** `<pasta-de-ferramentas>\gemini-tts\gemini_tts.py` (subcomandos `vozes` · `falar` · `desenhar` · `clonar`; `--voz <apelido>` = atalho para uma voz clonada). Chama a REST direto porque o `google-genai` 2.6.0 instalado **não tem `voices`**.

## Veredito do autor (⏳ reconferir a partir de jan/2027: é veredito sobre ferramenta)

> Preferência do autor: é um teste de ouvido dele, às cegas. O que é medido (cota, preço, o que a API faz e não faz, o erro 500 no free) está nas outras seções.

- Ouvindo às cegas, ele não soube dizer que era IA. Chamou de **"campeão invicto"** contra ElevenLabs v3 e Multilingual v2 (o `multilingual_v2` é o que o `<cliente-ótica>` usa hoje) no mesmo texto.
- A clone da voz do autor assustou o próprio autor: *"jurava que tava ouvindo sample meu"*. **Talvez não use para vídeo longo do canal**, mas vê várias utilidades. Usar a clone em peça é decisão DELE (o dono da voz), caso a caso. Não usar por conta própria.
- ⇒ **Texto → voz natural PT-BR = Gemini 3.8 por padrão.** Isso supera o "Kokoro/Chatterbox" do `<pasta-de-ferramentas>/stack-ia` e a linha de TTS do [[reference_nvidia_nim]] (nota não incluída).

## O que ele tem
- **107 vozes NATIVAS pt-BR** na biblioteca (`GET /v1beta/voices?language_code=pt-BR`, ids `pt-br-storyteller-6`, `pt-br-commercial-4`…). Todas vêm marcadas "Brazilian Portuguese" genérico, **sem sotaque regional**. Preferir as `pt-br-*` às 30 globais (Charon, Kore…). A família dessas globais é a mesma dos presets que puxaram sotaque inglês no Flow ([[project_gflow]], nota não incluída; ver a skill `gflow`); no TTS isso ainda não foi medido.
- **Voice design:** descrição em texto → `voice_id` fixo. Sotaque regional só sai por aqui. O teste "narrador com sotaque regional" improvisou gírias da região sozinho; o sotaque em si é o usuário quem julga.
- **Tags no meio do texto em PT funcionam** (`<laugh>`, `<sigh>`, `<long pause>`…). Transcrevi e nenhuma foi falada em voz alta. Tom por fala = campo `style`. Diálogo: no máximo 2 vozes.
- **Limite de vozes salvas:** 200 por projeto (desenhadas + clonadas), TTL de 1 ano, e **a voz só existe no projeto da chave que a criou**.

## O que ele NÃO tem
- **Voice changer / speech-to-speech / dublagem no timing.** A entrada do modelo é **só texto**. Para "minha performance, outra voz", segue o **ElevenLabs Voice Changer**. O `gemini-3.5-live-translate-preview` recebe áudio, mas **traduz** ao vivo (streaming, preview, voz instável). Não é troca de voz.

## Vozes clonadas (como o autor organizou)
- A voz do próprio autor foi clonada no projeto da **chave PAGA** e expira em **24/09/2027** (TTL de 1 ano). Ele guardou a amostra de 26,6 s e a gravação do consentimento numa pasta local (`<pasta-do-usuario>\...`). Com esses dois arquivos, dá para recriar a voz quando expirar.
- Qualquer um com a chave paga gera áudio com essa voz. Se o dono da voz pedir para apagar: `DELETE /v1beta/voices/<voice_id>`.
- Frase de consentimento pt-BR (literal da doc): *"Eu sou o proprietário desta voz e autorizo o Google a usá-la para criar um modelo de voz sintética."* O autor falou "dessa" (em vez de "desta") e **passou**.
- Uma segunda voz, de um colaborador, foi clonada na mesma chave paga (expira **06/10/2027**): amostra de 46 s + consentimento dele (frase oficial), **ambos áudio de WhatsApp**. Áudio de WhatsApp a 16 kHz clonou bem.
- Para a voz do colaborador vale a mesma regra: usar em peça é decisão do autor, caso a caso.

## Pegadinha + como conferir
- 🔴 **Clonagem na chave FREE devolve `500 INTERNAL` (não 403).** Parece servidor fora do ar, e tem gente no fórum oficial achando isso. Testei 3× em 2 projetos free, tudo 500; **na paga passou de primeira**. Voice design e síntese funcionam no free. Mecanismo geral em [[reference_gemini_keys]] (nota não incluída).
- **Cota free de TTS** (⏳): **10 req/DIA e 3/min por modelo**, visto no AI Studio em 24/09. Dá para teste, não para lote.
- **Preço** (⏳ **DOBRA em 01/01/2027**): Flash ~US$ 0,0135/min de áudio, Lite ~US$ 0,009/min. As 3 falas clonadas custaram ~US$ 0,01.
- **Conferir:** `python gemini_tts.py vozes --genero male` deve listar vozes `pt-br-*`. Se `falar --voz <apelido>` der 404, a voz expirou ou a chave paga mudou de projeto → recriar com `clonar`.
