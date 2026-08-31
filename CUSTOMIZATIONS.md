# Customizações — R36H / ArkOS (Fernando)

Registro do que foi pedido e aplicado neste handheld (ArkOS, tela **640×480**).  
Partições no Windows: **`G:`** = BOOT (FAT32), **`H:`** = EASYROMS (exFAT).  
SSH (com Remote Services ligado): `ark` / `ark`.

---

## 1. Tema EmulationStation

| Item | Valor |
|------|--------|
| Tema | `es-theme-art-book-next-classic` (este repo) |
| Pasta no cartão | `H:\themes\es-theme-art-book-next-classic` → `/roms/themes/...` |
| Aspect | **4:3** |
| Conteúdo | System list = Art Book **Next** ArkOS · Gamelist = Art Book **3:2** classic |
| Arte | GBA/GBC atualizados no repo |

UI Settings → Theme Set → este tema.

---

## 2. Logo de boot (u-boot) — antes do Linux

| Item | Caminho |
|------|---------|
| Ativo | `G:\logo.bmp` |
| Pool | `G:\BMPs\` (BMP 640×480) |
| Scripts | `H:\tools\Random Boot Logo.sh`, `Random Boot Logo Auto.sh`, `Random Boot Logo Auto Off.sh` |
| Helper | `H:\launchimages\randomize-bootlogo.sh` |

Pool (webp → BMP), exemplos: Super Game Boy (várias), Reverse SGB, SIES, Game Boy Mini Black, etc.

O sorteio vale para o **próximo** boot (u-boot lê o BMP antes do Linux).

---

## 3. Launch image (ao abrir um jogo)

| Item | Caminho |
|------|---------|
| Ativo | `H:\launchimages\loading.jpg` (ou `.gif` / `.mp4` conforme o modo) |
| Pool | `H:\launchimages\pool\{jpg,gif,mp4}\` |
| Scripts | `H:\tools\Random Launch Image.sh`, `… Auto.sh`, `… Auto Off.sh` |

UI Settings → Game Loading Image Mode = Pic/GIF/Video + Default.

---

## 4. Splash do EmulationStation (depois do boot Linux)

### Arquivos

| Item | Caminho |
|------|---------|
| Splash ativo | `/usr/bin/emulationstation/resources/splash.png` (tela cheia 640×480) |
| Conteúdo atual | **Reverse Super Game Boy** |
| SVG | `splash.svg` neutralizado (preto); não é o path ativo |
| Cópia no cartão | `H:\tools\ES-logo-changer\` (PNG/SVG de referência) |

### Binário ES (`/usr/bin/emulationstation/emulationstation`)

Patches aplicados (backups `*.bak-*` ao lado do binário):

| Patch | Efeito |
|-------|--------|
| Strings de status | `"Loading..."`, `"Starting UI"`, etc. → espaços |
| `SplashScreenProgress` / progresso | nomes tipo favorites/retroarch não listados no progresso (setting + lógica) |
| Escala do splash | `0.4` → **`1.0`** (imagem ocupa a tela) |
| Path do splash | `:/splash.svg` → **`:/splash.png`** (nanosvg não embute PNG em SVG) |
| `endRenderLoadingScreen` | **`ret` imediato** — não zera o splash entre fases (evita flash do logo pequeno) |

### Settings (`~/.emulationstation/es_settings.cfg`)

- `SplashScreen` = true  
- `SplashScreenProgress` = false  

---

## 5. Plymouth (tela “sistema carregando” / ArkOS version)

| Item | Detalhe |
|------|---------|
| Problema | Texto `ArkOS 2.0 (11072025)(AeUX)` + fundo preto do tema `ubuntu-text` |
| Título | `title=` vazio em `/usr/share/plymouth/themes/text.plymouth` (backup `*.bak-arkostitle`) |
| Tema gráfico | `/usr/share/plymouth/themes/pslogo/` (`script.so`) |
| Imagem | `splash.png` 640×480 — **Super Game Boy Mono Kanji** (tela cheia) |
| Ativação | `text.plymouth` / alternatives apontam para `pslogo` |
| Dependência instalada | `librsvg2-bin` (conversão SVG→PNG na época do logo PS) |

---

## 6. Linha do tempo visual (ordem no boot)

```
1. u-boot          → G:\logo.bmp          (boot logo, pode ser aleatório)
2. Plymouth        → pslogo/splash.png    (SGB Mono Kanji, sem texto ArkOS)
3. EmulationStation→ resources/splash.png (Reverse SGB, full screen, sem textos)
4. Ao abrir jogo   → launchimages/loading.* (pool aleatória)
```

---

## 7. Outros (contexto do cartão)

- Limpeza de ROMs (idioma / fama / pastas aninhadas) na partição EASYROMS — não faz parte do tema git, só do cartão.
- Remote Services no Options para SSH após reinícios do ES.

---

## 8. Como reverter (resumo)

| Área | Como |
|------|------|
| Tema ES | Outro Theme Set no menu |
| Boot BMP | Restaurar `G:\logo.bmp` / desligar Auto |
| Launch | Auto Off + `loading.jpg` fixo |
| Splash ES | Restaurar `emulationstation.bak-splashpng` / `bak-splashsize` / `bak-noflash` e `splash.svg` dos `.bak-*` |
| Plymouth | Restaurar `text.plymouth.bak-arkostitle` e theme ubuntu-text |

---

## 9. Manutenção deste repo

- Rebuild do híbrido: `_build_hybrid.py` (se presente).
- Arte de sistemas: `_inc/systems/`.
- Este arquivo (`CUSTOMIZATIONS.md`) descreve o **aparelho**; o tema em si continua em `theme.xml` + `_inc/`.
