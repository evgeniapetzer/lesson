Вот содержимое — скопируй и вставь целиком:

```
# CLAUDE.md

## Project Overview

**Карьерная молекула v2** — standalone single-file web application for career competency assessment. Evaluates professionals across 5 competencies, compares salary to market data, and generates personalized 90-day development plans.

**Entry point:** `career-molecule-v2.html` (1,300+ lines, all HTML/CSS/JS in one file)

## Architecture

Single HTML file with three embedded sections:

```
<head>
  └── External: Chart.js 4.4.0 (CDN), Google Fonts (DM Serif Display, DM Sans)
  └── <style> — ~650 lines of CSS

<body>
  ├── #s1 — Landing/Hero
  ├── #s2 — Direction selection
  ├── #s25 — Salary input
  ├── #s3 — Assessment quiz (20 questions)
  └── #s4 — Results (radar chart + recommendations)

  └── <script> — ~575 lines of vanilla JS (ES6+)
```

Screen navigation is SPA-style: `goTo(id)` toggles `.active` class on `.screen` elements — no routing library.

## Key Data Structures

All data is defined as JS constants near the top of the `<script>` block:

| Constant | Purpose |
|----------|---------|
| `COMPETENCES` | Array of 5 competency names |
| `QUESTIONS_RAW` | 20 questions, each with `comp` index and 3 scored answers (1–3) |
| `SALARY_DATA` | Market salary ranges for Middle / Strong Middle / Senior |
| `LEVEL_INFO` | Descriptive text per level |

## Scoring Logic

- 20 questions × max 3 points = 60 total points
- Level thresholds: Middle ≤ 34, Strong Middle ≤ 48, Senior Readiness 49+
- Per-competency average = sum of scores ÷ 4 questions per competency
- Radar chart rendered by `renderRadar(compAvg)` using Chart.js

## State Variables

```js
let currentQ = 0;   // current question index
let answers = {};   // { qIndex: score }
let userSalary = null;
```

State is reset entirely by `restart()`.

## CSS Design System

CSS custom properties defined at `:root`:

```css
--navy: #0f1c2e
--teal: #2ec4b6
--orange: #f97316
--radius: 16px
```

Typography: `DM Serif Display` for headings, `DM Sans` for body. Animations: `fadeUp`, `scaleIn`, `barGrow`, `pulse`, `slideIn`.

## No Build Tooling

There is no `package.json`, no bundler, no transpiler, no linter config, no test runner. The file is production-ready as-is — open in any modern browser.

To "run" the project:
```bash
# Open directly in browser
open career-molecule-v2.html

# Or serve locally
python3 -m http.server 8080
```

## Development Guidelines

### Editing the file

- All changes go into `career-molecule-v2.html`
- Keep CSS in the `<style>` block, JS in the `<script>` block at the bottom
- Data constants (`QUESTIONS_RAW`, `SALARY_DATA`, etc.) live at the top of `<script>`
- Business logic (scoring, recommendations) goes in named functions

### Adding questions or competencies

1. Add competency name to `COMPETENCES` array
2. Add questions to `QUESTIONS_RAW` with the correct `comp` index
3. Update `SALARY_DATA` / `LEVEL_INFO` if thresholds change
4. Adjust level threshold constants in `showResults()`

### Modifying the radar chart

The Chart.js config is inside `renderRadar(compAvg)`. Labels are derived from `COMPETENCES`. Destroy existing chart before re-rendering: `radarChart.destroy()`.

### Salary feature

`userSalary` is set by `confirmSalary()` or left `null` if skipped. Salary comparison logic lives entirely in `showResults()`.

### Adding a new screen

1. Add `<section class="screen" id="sN">` in `<body>`
2. Add CSS under `/* ===== SCREEN N ===== */` comment
3. Call `goTo('sN')` to navigate to it

## External Dependencies

| Dependency | Version | Source |
|-----------|---------|--------|
| Chart.js | 4.4.0 | cdnjs CDN |
| DM Serif Display | — | Google Fonts |
| DM Sans | 300–700 | Google Fonts |

The app requires internet access to load fonts and Chart.js. For offline use, bundle these locally.

## Don't

1. **Don't split the single HTML file into separate files.**
   Portability is the core architectural property — the file opens via `file://` with no server.
   _Example: extracting CSS into a separate file breaks the app when opened directly in a browser._

2. **Don't introduce a bundler, npm, or build step.**
   No `package.json`, no `node_modules`, no pipeline. Adding tooling creates infrastructure overhead with zero benefit for this project.
   _Example: adding Prettier pulls in npm init → .eslintrc → 200 MB of node_modules for a 1-file project._

3. **Don't hardcode hex colors — use CSS variables.**
   All colors are defined as custom properties at `:root`. Hardcoding breaks the design system and makes theme changes require dozens of edits.
   _Example: changing brand teal requires a global find-replace instead of editing one line in `:root`._

4. **Don't modify `QUESTIONS_RAW` without updating scoring thresholds.**
   Adding or removing questions changes the maximum possible score (currently 60). The level thresholds in `showResults()` must be recalculated or results will be systematically wrong.
   _Example: adding a 6th competency raises max to 72, but Senior threshold stays at 49 — dropping the bar from 82% to 68% silently._

5. **Don't call `renderRadar()` without destroying the existing chart first.**
   Chart.js throws "Canvas is already in use" and renders overlapping artifacts if the previous instance isn't destroyed.
   _Example: user clicks "restart" → `renderRadar()` runs again → two radar charts stacked on the same canvas, console error._

## What Claude Should Know

- **The file is the product.** There is no build output. Edit `career-molecule-v2.html` directly. The browser is the runtime.
- **All scoring logic is co-located in `showResults()`.** Level determination, salary comparison, gap analysis, and recommendations all live in this single function — changes to questions, thresholds, or salary bands must be reflected here.
- **Screen IDs are load-bearing.** `goTo(id)` navigates by matching element IDs (`s1`, `s2`, `s25`, `s3`, `s4`). Renaming a screen ID without updating all `goTo()` calls will silently break navigation.
```

После вставки нажми **Commit changes** — и скажи мне, появилась ли кнопка. Тогда создам PR.
