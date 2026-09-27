# AGENT_GUIDE.md — Technical Guide for AI Agents & Maintainers

This document is written for **AI agents and maintainers** who need to understand, verify, or extend this repository. Read it fully before modifying anything.

---

## 1. What this project is

A **single-file static HTML application** (`index.html`, ~1.3 MB) that combines:

1. **PCM4 tutorials** — CarPlay activation instructions for Porsche PCM4 (MIB2 High) units, powered by the M.I.B. toolbox (GEM 4.12st).
2. **PCM5 mod toolkit** — a working client-side tool for LawPaul's MH2p CarPlay fullscreen/windowed mods: firmware version checking, fallback analysis, and jar generation from uploaded vehicle files.

**Deployment contract:** zero build steps. `index.html` is fully self-contained (CSS, JS, data, images as base64). Host it statically (GitHub Pages / Netlify / file://) and it works.

---

## 2. Architecture & code layout

All code lives inside `index.html`:

- **CSS** — two scoped blocks:
  - Root styles (`.wrap`, `.tabs`, `.card`, etc.) for the landing + PCM5 pages.
  - `#gem-root`-scoped styles (`.gem-*` classes) for the PCM4 tutorial area, including `@media (max-width:720px)` mobile rules at the end.
- **JS** — 5 `<script>` blocks (in order):
  1. **Data**: version-coverage database + install scripts (`MH.DB`-like object: `{full:[...], windowed:[...]}` with per-version class hashes; `COVERED`; `SCRIPTS` for install/uninstall shell code).
  2. **PCM5 UI logic**: tab switching, version check, upload analysis, jar generation, downloads.
  3. **GEM (PCM4) logic**: `gem_showPage(n)` page switching.
  4. Platform routing: `choose(id)` / `backLanding()`.
  5. Mobile / misc handlers.
- **Images** — all screenshots are inline base64 data URIs (no external fetches).

**No external network calls at runtime.** All links (GitHub, mibsolution.one) are user-facing tutorial links only.

---

## 3. Page structure & routing

```
index.html
└── section#landing (default visible)
    ├── card PCM4  → choose('page-gem')
    └── card PCM5  → choose('page-pcm5')

PCM4 section#page-gem
└── #gem-root
    ├── .gem-tabs (3 buttons)  → gem_showPage(1|2|3)
    ├── #gem-page1  Standard Flow (with patches)
    ├── #gem-page2  On-vehicle Self-contained (no version needed)
    └── #gem-page3  Panamera Pog24 · Fullscreen/Windowed

PCM5 section#page-pcm5
├── nav.tabs (2 top tabs)  → ① CarPlay Activation Tutorial / ② Fullscreen · Windowed Mod Tool
└── inside ②: nav.tabs (3 sub-tabs)  → ① Version Check / ② Uncovered: extract & install / ③ Upload & Generate
```

**Important scoping rules (fixed bugs, do not regress):**
- `.gem-tab` active state uses the class **`gem-active`** (`#gem-root .gem-tab.gem-active{...}`). The switching JS must set `'gem-active'` — an earlier version set `'active'`, which silently broke the selected-state styling.
- `gem_showPage` toggles `#gem-page1/2/3` display and updates `.gem-tab` classes.
- PCM5 inner tabs are scoped with direct-child selectors (`#page-pcm5 > section.panel`, `#page2 > section.panel`) so they never hide panels in other sections.

---

## 4. Business logic (PCM5 core)

### 4.1 Version check
Input format: `MH2p_<REGION>_<OEM>_<TYPE>_<P|K><NNNN>`, e.g. `MH2p_ER_PO416_P2870`.

- If the version matches a database entry → **covered** → user downloads official mod files directly.
- If not covered → the tool explains the **fallback rule**:
  > Porsche PO416 / POG35 uncovered versions automatically fall back to `PO416_P2491` (hardcoded in official `install.sh`).
- The info banner states firmware evolution: **98xx (early) → 24xx → 26xx → 28xx (latest)**. Note: 98xx segment is only *partially* covered (P9829 exists for POG35, not PO416 — those roll back to P2491), while the 28xx segment is fully covered.

### 4.2 Upload analysis (uncovered versions)
User uploads `lsd/jars` extracted from the unit (e.g. `fc.jar`, `lsd.jar`). The tool:
1. Parses each jar and computes class hashes for the 3 modified classes: `de/audi/app/terminalmode/adi/ADITMConfiguration.class`, `de/audi/tghu/terminalmode/hmi/pag2pg35/TerminalModeScreenBag1.class`, `de/esolutions/hmi/widgets/pgen2/utils/WidgetUtilsPGen2.class`.
2. Matches against the database to find byte-identical known versions → generates the matching `fc-full-<VERSION>.jar` / `fc-windowed-<VERSION>.jar` + install script.
3. If no identical match, it reports whether the fallback (`PO416_P2491`) is structurally compatible.

### 4.3 Terminology lock (do not flip)
- **`windowed` = 全屏** — `fc-windowed-*.jar`
- **`full` = 满屏** — `fc-full-*.jar`

(User-confirmed mapping: widescreen = fullscreen-style, fullscreen = fully expanded.)

---

## 5. Locked content decisions (user-approved, do not revert)

| Item | Locked statement |
| --- | --- |
| PCM4 platform naming | Only Porsche host codes: **POG11 / POG24**. No BYG24, no MHI2Q, no other VW/Audi codes anywhere in visible text (paths inside M.I.B. naming rules like `MHI2_CN_POG24_..._PATCH` are genuine tool paths and must stay). |
| Panamera Pog24 page | Applies to **all firmware versions starting with POG24**; requires **M.I.B. (GEM 4.12st) only, no external patch pack**. Flow: ① System update → ② Debug page (`m.i.b`) → ③ `multimedia_system` → ④ `porsche` → `pog24` → ⑤ Enable `widescreen` (fullscreen) or `fullscreen` (full) CarPlay → auto install & reboot. |
| Pog24 screenshots | Raw screenshots already carry the user's own red circles/highlights — **no overlay marks** (`gem-mark`/`gem-num`) are added on page 3. |
| Tab labels | PCM4: `📋 标准流程（有对应补丁）` / `🔧 车机自闭环（无需版本号）` / `🚗 帕梅 Pog24 · 全屏/满屏`. PCM5: `① CarPlay 激活教程` / `② 全屏 · 满屏 Mod 工具`; sub-tabs `① 版本检查` / `② 未覆盖版：提取 · 安装` / `③ 上传分析 · 生成`. |
| Wording style | Official/neutral tone, no conversational filler, no "页面 1/2" pagination labels, no Audi mentions, no "Wireless CarPlay unavailable in China" statements. |
| M.I.B. patch pack | Downloaded separately from mibsolution.one (`20230926_M.I.B_Patches.7z`, 552 MB). **Not** included in this repo. GEM.pkg is not open-source; the AIO self-contained flow is not source-verified — keep honest annotations. |

---

## 6. How to deploy (for any agent)

**Static hosting — no build, no dependencies:**

```bash
# Local check
open index.html            # or just double-click

# GitHub Pages
git init && git add . && git commit -m "deploy"
git remote add origin https://github.com/<user>/<repo>.git
git push -u origin main
# Settings → Pages → Deploy from branch: main / (root)
```

The site will be at `https://<user>.github.io/<repo>/`.

**Deployment acceptance criteria:**
- `index.html` loads without console errors.
- No external resource fetches fail (there should be none at runtime).
- Landing page shows both platform cards; both routes work.

---

## 7. Verification checklist (for agents doing QA)

Run in a browser (desktop ≥ 1280 px **and** mobile ≤ 720 px — the `@media (max-width:720px)` block changes layout):

1. **Landing**: PCM4 + PCM5 cards render, clicking routes correctly, `← 返回平台选择` returns.
2. **PCM4**: 3 tabs switch with a visible dark selected state (`gem-active`); all 3 pages render; no `页面 1/2` text; no BYG24/MHI2Q text.
3. **Pog24 page**: 5 steps, 4 images, **0 overlay marks**, legend/caption under each image; text includes `widescreen 为全屏，fullscreen 为满屏`.
4. **PCM5 top tabs + sub-tabs**: all switch correctly without hiding other sections' panels.
5. **Version check**: input `MH2p_ER_PO416_P2870` → "该版本已被覆盖"; input an uncovered version → fallback info shown; info banner shows `98xx（早期）→ 24xx → 26xx → 28xx（最新）`.
6. **Console**: zero errors on every route.
7. **Mobile**: no horizontal overflow; tabs full-width; images scale; tables scroll horizontally.

---

## 8. Maintenance guide

### Adding a new PCM5 covered version
1. Obtain the official mod jars for the version (`fc-full-<V>.jar`, `fc-windowed-<V>.jar`) from LawPaul's repos / community.
2. Extract the 3 class files from each jar and compute their MD5 hashes.
3. Add an entry to the `full` and `windowed` arrays in the embedded database (script block 1) with `f`, `mod`, `v`, and `e` (array of `{p, h}` class paths + hashes).
4. Optionally update the `cls` group id so structurally identical versions reuse each other.
5. Re-run the verification checklist (especially the version check for the new version).

### Editing the Pog24 tutorial
- Content lives in `#gem-page3` inside `index.html`.
- Images are base64-inline; to replace one, re-encode the new screenshot as a data URI (original raw files are in `assets/pog24-screenshots/`).
- Keep: no overlay marks, legend/caption under each figure, numbered steps ①–⑤ continuous, M.I.B.-only statement.

### Editing PCM4 (GEM) content
- Everything is inside `#gem-root`; use only `gem-*` classes scoped to `#gem-root` (never bare element selectors that could leak to the rest of the page).

---

## 9. Quick facts

| Fact | Value |
| --- | --- |
| Entry file | `index.html` (~1.3 MB, self-contained) |
| Runtime deps | None |
| Build step | None |
| Version DB location | JS script block 1 (`MH.DB`-like objects) |
| PCM4 CSS scope | `#gem-root` |
| Mobile breakpoint | `max-width:720px` |
| Fallback version | `PO416_P2491` (both mods) |
| Version evolution | 98xx (early) → 24xx → 26xx → 28xx (latest) |
