# Porsche CarPlay Tool (PCM4 / PCM5)

> 保时捷车机 CarPlay 激活 + 全屏/满屏 Mod 一站式工具 · 在线使用：**https://stormeye818.github.io/porsche-carplay-tool/**
>
> [中文](#中文说明) | [English](#english)

---

# 中文说明

单文件、纯离线的 HTML 工具箱，用于保时捷车机的 **Apple CarPlay 激活与全屏/满屏改装**：

- **PCM4**（MIB2 High，POG11 / POG24）——基于 **M.I.B.** 工具箱（GEM 4.12st）的 CarPlay 激活分步教程。
- **PCM5**（MH2p，PO416 / POG35）——基于 **LawPaul 的 MH2p** 开源项目的全屏/满屏 Mod 工具箱：固件版本检查、fallback 校验、jar 生成。

全部内容（样式、脚本、版本库、截图）内嵌在单个 `index.html` 中，无需构建、无外部依赖、无网络请求，打开即用，也可托管到任意静态服务器。

> 非官方社区项目，按 CC BY-NC-SA 4.0（非商业）提供，刷写/编码车机有风险（变砖、影响质保），后果自负。

## 适用车型

| 平台 | 车机 | 常见车型 | 对应功能 |
| --- | --- | --- | --- |
| **PCM4** | MIB2 High（POG11 / POG24） | 981/718/991、Macan、Panamera（Pog24）等 | CarPlay 激活教程（M.I.B.） |
| **PCM5** | MH2p（PO416 / POG35） | 新款 Panamera / Cayenne / Taycan 等 | 全屏/满屏 Mod 工具（MH2p） |

## 怎么用

1. 打开[在线页面](https://stormeye818.github.io/porsche-carplay-tool/)（或本地双击 `index.html`）→ **选择你的平台**：PCM4 或 PCM5。
2. **PCM4**：按你的情况选教程 tab——
   - `📋 标准流程（有对应补丁）`：完整 7 步，需另下 M.I.B. 工具箱 + 补丁包（mibsolution.one，552 MB）；
   - `🔧 车机自闭环（无需版本号）`：与版本无关，由车机自行备份固件并生成补丁；
   - `🚗 帕梅 Pog24 · 全屏/满屏`：**仅 POG24**，只需 M.I.B.、**无需外部补丁包**，经 GEM 调试页开启 widescreen/fullscreen CarPlay。
3. **PCM5**：先看激活教程，再用版本检查和生成工具。若版本未覆盖，按页内 USB-LAN 教程提取车机 jar 文件后上传分析。
4. 所有教程内容、下载链接和风险提示都在页面内。

## 术语约定（全工具统一）

- **windowed = 全屏** —— `fc-windowed-*.jar`
- **full = 满屏** —— `fc-full-*.jar`
- widescreen 指全屏样式显示，fullscreen 指完全展开显示。

## 数据与许可

- PCM5 Mod 数据来自 **LawPaul**（github.com/LawPaul/MH2p_*），许可 CC BY-NC-SA 4.0。
- PCM4 教程基于 **M.I.B.** 项目（github.com/Mr-MIBonk/M.I.B._More-Incredible-Bash），GEM 截图来自其公开 wiki。M.I.B. 补丁包（`20230926_M.I.B_Patches.7z`，552 MB）需从 mibsolution.one 单独下载，**不在本仓库内**。
- 本工具与 Porsche 官方无关。

## 反馈与维护

PCM5 缺版本或内容修正，请看 **[AGENT_GUIDE.md](AGENT_GUIDE.md)**（含数据存放位置与更新方法，亦供 AI Agent/维护者阅读）。

---

# English

A single-file, offline HTML toolkit for enabling and modding **Apple CarPlay** on Porsche infotainment systems:

- **PCM4** (MIB2 High, POG11 / POG24) — step-by-step CarPlay activation tutorials driven by the **M.I.B.** toolbox (GEM 4.12st).
- **PCM5** (MH2p, PO416 / POG35) — CarPlay fullscreen / windowed mod toolkit based on **LawPaul's MH2p** open-source project: firmware version check, fallback verification, and jar generation.

Everything (CSS, JS, version database, screenshots) is embedded in a single `index.html`. No build step, no external dependencies, no network calls — it works offline and can be hosted on any static web server (GitHub Pages, Netlify, etc.).

Live: **https://stormeye818.github.io/porsche-carplay-tool/**

> Non-official, community project. Provided under CC BY-NC-SA 4.0 (non-commercial). Use at your own risk.

---

## Features

### PCM4 — CarPlay Activation Tutorials (M.I.B.)
Three tabs:

| Tab | Description |
| --- | --- |
| 📋 Standard Flow (with patches) | Full 7-step flow: download M.I.B. toolbox → download patch pack (mibsolution.one, 552 MB) → arrange SD card → install via vehicle update → GEM patch + CarPlay activation |
| 🔧 On-vehicle Self-contained (no version needed) | Version-independent flow: the unit backs up its own firmware image and generates patches on-device |
| 🚗 Panamera Pog24 · Fullscreen/Windowed | **POG24-only** flow: system update → GEM debug page → `multimedia_system` → `porsche/pog24` → enable widescreen/fullscreen CarPlay. Requires M.I.B. only, **no external patch pack** |

### PCM5 — Fullscreen / Windowed Mod Toolkit (MH2p)

| Section | Description |
| --- | --- |
| ① CarPlay Activation Tutorial | MH2p activation walkthrough (software update via USB/SD, engineering menu) |
| ② Fullscreen · Windowed Mod Tool | ① Version Check → ② Uncovered versions: extract & install → ③ Upload & Generate |

Core logic:
- **Version check** — tells whether your firmware version is covered by published mod files (`fc-full` = fullscreen / windowed, `fc-windowed` = windowed / windowed).
- **Fallback rule** — any uncovered Porsche version (PO416 / POG35) automatically falls back to the `PO416_P2491` fallback files at install time (hardcoded in the official `install.sh`).
- **Upload analysis** — upload the `lsd/jars` extracted from your unit; the tool compares class hashes against the built-in database to find an identical known version or verify whether the fallback will work.

> Terminology (locked): **windowed = 全屏** / **full = 满屏** — kept consistent across the whole tool. Widescreen = fullscreen-style display, fullscreen = fully expanded.

---

## Quick Deploy (GitHub Pages)

1. Create a new repository on GitHub (public or private).
2. Upload the contents of this folder — at minimum `index.html`.
3. Go to **Settings → Pages**, set **Source** to `Deploy from a branch`, branch `main`, folder `/ (root)`.
4. Save. Your tool is live at `https://<user>.github.io/<repo>/`.

No build step required. `index.html` is self-contained.

Any static host works the same way: Netlify, Cloudflare Pages, Vercel, or just opening `index.html` locally in a browser.

---

## Repository Structure

```
porsche-carplay-tool/
├── index.html                  # The complete tool (single-file, self-contained)
├── README.md                   # This file
├── AGENT_GUIDE.md              # Technical deep-dive for AI agents / maintainers
└── assets/
    └── pog24-screenshots/      # Original raw screenshots used in the Pog24 tutorial
        ├── gem-debug-menu.jpg            # GEM 4.12st debug menu (green page, m.i.b entry)
        ├── mib-main-menu.jpg             # M.I.B. main menu (multimedia_system entry)
        ├── multimedia-porsche-menu.png   # multimedia_system → porsche submenu (pog24)
        └── pog24-widescreen-page.png     # pog24 page (Enable widescreen CarPlay)
```

---

## Usage Summary

1. Open the page → **choose your platform**: PCM4 or PCM5.
2. **PCM4**: pick the tutorial tab that matches your situation (standard flow / on-vehicle self-contained / Panamera Pog24).
3. **PCM5**: follow the activation tutorial, then use the version check and generator. For uncovered versions, extract your unit's jars (see the built-in USB-LAN tutorial) and upload them for analysis.
4. All tutorial content, download links and warnings are inside the page itself.

---

## Data & License Notes

- PCM5 mod data originates from **LawPaul** (github.com/LawPaul/MH2p_*), licensed CC BY-NC-SA 4.0.
- PCM4 tutorial content is based on the **M.I.B.** project (github.com/Mr-MIBonk/M.I.B._More-Incredible-Bash); GEM screenshots come from its public wiki. The M.I.B. patch pack is downloaded separately from mibsolution.one (552 MB) and is **not** included in this repo.
- This is a community tool — not affiliated with Porsche. Flashing/coding a car computer carries risk (bricking, warranty). Always follow the in-page warnings.

---

## Feedback & Maintenance

For missing PCM5 firmware versions or content fixes, see **[AGENT_GUIDE.md](AGENT_GUIDE.md)** for exactly where data lives and how to update it.
