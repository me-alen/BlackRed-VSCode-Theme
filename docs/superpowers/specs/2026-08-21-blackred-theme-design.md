# BlackRed — VS Code Theme

**Status:** Approved · **Date:** 2026-08-21 · **Target version:** 0.1.0

---

## 1. Context

`BlackRed-VSCode-Theme` was scaffolded with `yo code` and then abandoned. Nothing beyond the
scaffold was ever written:

- `themes/BlackRed-color-theme.json` still holds the generator's default Material palette
  (`#263238` blue-grey canvas, purple/blue/green tokens). It contains no red.
- The `colors` block sets **4** workbench keys out of roughly 250. The entire UI chrome is unthemed.
- `README.md` is the boilerplate "Working with Markdown" placeholder.
- `package.json` has no publisher, icon, license, repository, or keywords.

The only surviving statement of intent is the package description: *"Pure Black and a tint of Red."*

This spec covers building the theme for the first time, not modifying an existing one.

## 2. Goals

1. A complete, readable dark theme on a true-black canvas with red as its signature.
2. Full workbench coverage — not just the editor.
3. A sandbox that makes checking a color change take seconds, not a rebuild.
4. A README that doubles as the project's progress tracker.
5. Marketplace-ready packaging.

## 3. Non-goals

- Light or high-contrast variants. One theme, done well.
- Italic/bold typography systems beyond italic comments.
- A palette build step or code generation. The theme JSON is hand-authored and committed;
  it is the single source of truth that both the preview and the validator read.

## 4. Palette — "Signal"

Chosen from a three-way visual comparison (`preview/index.html`) against two alternatives,
"Ember" (accent-only red) and "Inferno" (red-forward). Signal keeps red as the identity while a
single cool tone carries strings and types, so token classes stay distinguishable.

Two colors were corrected after contrast testing: `comment` (`#4A585E` → `#5C6B73`) and
`lineNumber` (`#374045` → `#525F66`). Both originally failed 3:1 against pure black.

### 4.1 Surfaces

| Token | Hex | Use |
|---|---|---|
| `canvas` | `#000000` | Editor background |
| `surface` | `#07090A` | Activity bar, sidebar, tab bar, title bar, panel |
| `raised` | `#0E1113` | Hover states, inputs, widgets, dropdowns |
| `border` | `#171B1D` | Standard dividers |
| `border-strong` | `#232A2D` | Focus rings, active separators |

### 4.2 Syntax and UI text — all verified against `#000000`

| Role | Hex | Contrast | Floor |
|---|---|---|---|
| Foreground / variable | `#DDE3E6` | 16.20 | 4.5 |
| Function | `#EDE4DA` | 16.71 | 4.5 |
| Property | `#C3CDD1` | 12.97 | 4.5 |
| Type / class | `#9FC7D4` | 11.59 | 4.5 |
| String | `#7FA8B8` | 8.20 | 4.5 |
| Number / constant | `#C98F5A` | 7.56 | 4.5 |
| Decorator | `#D4796B` | 6.75 | 4.5 |
| **Keyword (accent)** | `#E63946` | 5.04 | 4.5 |
| Error | `#FF4A57` | 6.37 | 4.5 |
| List foreground | `#B6C0C4` | 11.33 | 4.5 |
| Active line number | `#CBD4D8` | 13.95 | 3.0 |
| UI secondary text | `#8B979C` | 7.00 | 3.0 |
| Operator / punctuation | `#6E7C82` | 4.87 | 3.0 |
| Comment *(italic)* | `#5C6B73` | 3.81 | 3.0 |
| Line number | `#525F66` | 3.19 | 3.0 |

Code text is held to WCAG AA (4.5:1). Recessive chrome is held to 3:1. A stricter comment
option (`#667780`, 4.52:1) is documented in the README for users who want comments at full AA.

### 4.3 Accent ramp

| Token | Hex | Use |
|---|---|---|
| `red` | `#E63946` | Keywords, cursor, badges, active borders, status accents |
| `red-bright` | `#FF4A57` | Errors, deletions, invalid tokens |
| `red-dim` | `#B32B36` | Pressed states, inactive accent borders |
| `red-wash` | `#E6394633` | Selection, find-match background |

### 4.4 Terminal — all 16 ANSI colors, all ≥ 3:1 (except `black`, a background swatch)

| | Normal | | Bright | |
|---|---|---|---|---|
| black | `#171B1D` | 1.21 | `#525F66` | 3.19 |
| red | `#E63946` | 5.04 | `#FF4A57` | 6.37 |
| green | `#7FB88F` | 9.16 | `#9ACCA8` | 11.58 |
| yellow | `#C98F5A` | 7.56 | `#E0A972` | 10.09 |
| blue | `#7FA8B8` | 8.20 | `#9FC7D4` | 11.59 |
| magenta | `#C4879B` | 7.29 | `#DDA0B8` | 9.79 |
| cyan | `#9FC7D4` | 11.59 | `#BADCE5` | 14.46 |
| white | `#B6C0C4` | 11.33 | `#DDE3E6` | 16.20 |

## 5. Theme file

`themes/BlackRed-color-theme.json` grows from 4 workbench keys to full coverage across:

activity bar · sidebar · editor (incl. bracket-pair colorization, indent guides, whitespace,
rulers) · editor groups and tabs · status bar · title bar · panel · integrated terminal ·
lists and trees · inputs, dropdowns, buttons, badges · scrollbars and minimap · git decorations ·
diff and merge conflict · peek view · notifications · quick input and command palette · breadcrumbs.

Two additions the scaffold lacks entirely:

- `"semanticHighlighting": true`
- A `semanticTokenColors` block, so language servers that emit semantic tokens (TypeScript, Rust,
  Python) resolve to the same palette rather than falling back to TextMate approximations.

Token colors keep the scaffold's existing scope groups where they are correct and add coverage
for JSX/TSX, Vue SFC blocks, YAML/TOML keys, shell, SQL, and Dockerfile.

## 6. Sandbox

Two tiers, because they answer different questions.

### Tier 1 — fast loop (`npm run preview`)

`preview/index.html` renders a VS Code chrome replica: activity bar, sidebar tree, tabs,
breadcrumb, gutter, code, minimap, terminal panel, status bar. It currently carries three
hardcoded candidate palettes; it will be rewired to `fetch()` `themes/BlackRed-color-theme.json`
and drive every surface from the committed file, so the preview always shows what actually ships.

Served over HTTP (a `file://` page cannot `fetch` a sibling JSON). Answers "did that color change
land, and does it read well" in about a second.

**Deliberate limitation:** it is a replica, not VS Code. It cannot validate real TextMate scope
resolution. It is a color instrument, and Tier 2 is the source of truth.

### Tier 2 — faithful (`npm run dev`)

`code --extensionDevelopmentPath=$PWD` opens a real Extension Development Host with the theme
loaded. `.vscode/launch.json` already configures the same thing on F5. This is what gets checked
before any release.

### Guardrail (`npm run validate`)

A zero-dependency Node script that fails loudly on:

1. Invalid JSON.
2. Malformed hex values (anything not `#RGB`/`#RRGGBB`/`#RRGGBBAA`).
3. Workbench keys VS Code does not recognize (catches typos silently ignored at runtime).
4. Contrast regressions — re-runs the table in §4.2 and fails if any token drops below its floor.
5. Token scopes listed twice with conflicting colors.

Runs in `prepackage`, so a failing palette cannot be published.

## 7. README as tracker

Replaces the boilerplate entirely:

1. Title, one-line pitch, screenshot.
2. Install — Marketplace, and `.vsix` sideload.
3. **Roadmap** — the phase checklist in §9, kept current as work lands.
4. Palette table (§4.2) with the strict-AA comment note.
5. Sandbox — how to run both tiers and the validator.
6. Pre-publish checklist.
7. License.

## 8. Marketplace scaffolding

- `LICENSE` — MIT.
- `icon.png` — 128×128, black field with the `#E63946` accent.
- `package.json` — `publisher`, `icon`, `license`, `repository`, `bugs`, `homepage`, `keywords`,
  `galleryBanner: { color: "#000000", theme: "dark" }`.
- `@vscode/vsce` as a devDependency; `npm run package` and `npm run publish`.
- `CHANGELOG.md` in Keep a Changelog form, with a real `0.1.0` entry replacing the placeholder.
- `.vscodeignore` extended to exclude `preview/`, `scripts/`, and `docs/` from the shipped `.vsix`.

## 9. Phases

- **Phase 0 — Groundwork.** Palette lab built, three directions compared, Signal chosen,
  contrast verified. *Complete.*
- **Phase 1 — Core theme.** Workbench colors, token colors, semantic tokens.
- **Phase 2 — Tooling.** Preview rewired to the real theme; validator; npm scripts.
- **Phase 3 — Docs.** README, CHANGELOG, screenshots.
- **Phase 4 — Release.** License, icon, publisher metadata, package, publish.

## 10. Success criteria

1. `npm run validate` passes.
2. The Extension Development Host renders the theme with no unstyled (default-blue) chrome.
3. Every token in §4.2 meets its contrast floor.
4. `npm run package` produces a `.vsix` with no `vsce` warnings.
5. The README roadmap reflects actual state.

## 11. Open items

- **Publisher ID.** Publishing requires a publisher tied to an Azure DevOps account.
  `blackfly` is used as a placeholder and flagged in the README; it must be replaced before
  the first `vsce publish`. It blocks Phase 4 only.
- **Screenshots.** Phase 3 needs real Extension Development Host captures, not preview renders,
  since the Marketplace listing should show actual VS Code.
