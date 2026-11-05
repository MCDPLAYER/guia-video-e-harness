# Estilo PAPEL: stop-motion de recorte (explicador calmo, colagem Vox)

**Status:** ⏳ em teste. O autor, 15/10, sobre a v4: "ok, falta refino, mas pra um teste tá ótimo". Ainda sem peça aprovada pra postar.

**Peça:** um explicador do canal (34 s), `<pasta-de-renders>\<explicador-01>\` em modo `?papel` (`index.html`: `PAPEL`, `PFPS`, `PREV`).
- Os sprites são recortados dos 4 quadros Nano (rembg + inpaint, `sprites*.py`).
- Histórico do teste: `ref/EM-TESTE.md`, Registro 15/10.

## Quando
- Explicador de tom calmo/editorial, curiosidade, "como funciona", colagem estilo Vox.
- Não serve pra energia alta (32Bits) nem pra produto (UI).

## Gramática
- **Stop-motion de recorte:** pose segura 2 quadros (12 poses/s). O tempo de cada pose varia ±25–30 ms, o que tira a cara de máquina.
- **Tremor SÓ na peça que se mexeu nesta pose:** ±1,6 px / ±0,6°, medido contra a pose anterior.
  - A v3 tremia tudo e a câmera junto, e deu dor de cabeça no autor.
  - Nas refs ("Inspiração de edição") a peça quieta não treme.
- **Câmera lisa** (rostrum) a 24 q, sem tremor. Nas refs ela anda 0,01–0,03% da largura por quadro.
- **Sem blur e sem fade:** entra ou sai inteiro (opacidade 0/1). 24 fps sem tmix.
- **Borda cortada irregular.** A forma que cresce só re-recorta a borda quando muda de tamanho.
- **Movimento de papel, não de UI:** nada de curva lisa A→B. É pose a pose, imperfeito.
- Mantém do núcleo: evento a cada ≤0,6 s, câmera viva, todo estado com causa visível.

## Banidos
- Pixel ou qualquer outra estética aplicada como FILTRO por cima do papel. Cada estética compõe diferente; pixel pede roteiro e composição próprios (ver o teste de pixel nativo no Registro de `ref/EM-TESTE.md`).
- Tremor global.
- Câmera tremendo.
- Movimento interpolado liso.

## Falta (o "refino")
- Ninguém às cegas julgou ainda.
- Próximo passo: uma peça real curta, revisor contra uma referência de colagem da pasta "Inspiração de edição", e o usuário.
