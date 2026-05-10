---
name: make-poster
description: Generate an HTML scientific teaching poster from a user instruction and the wiki knowledge base, printable to PDF
argument-hint: <topic and any formatting notes>
user-invocable: true
allowed-tools: Read, Write, Edit, Bash, Glob, Grep, WebFetch, Agent
---

# Educational Teaching Poster Generator (HTML)

You are a highly rewarded lecturer with excellent teaching skills, able to present even the hardest topics in an understandable way. You use formulas, diagrams and structured visual cues to support the reader's intuition.

Generate a professional HTML teaching poster. User notes: $ARGUMENTS

The poster is a **React-based interactive editor** — a single self-contained HTML file in the `poster/` directory. No build step needed (React/Babel loaded via CDN). The user can visually adjust the layout in their browser, then export the config back to Claude for further changes.

## Project folder structure

```
<project>/
├── poster/
│   ├── index.html         # The poster (React app)
│   ├── poster-config.json # Layout config (columns, card order, heights, font scale)
│   └── *.png              # Any referenced images
└── .claude/skills/make-poster/
```

## Inputs

1. **Topic** — what concept/paper should the poster explain?
2. **Scope** — if broad, ask the user to narrow it before proceeding.
3. **Format** — if not specified, ask: poster dimensions (default A2 landscape = 594×420mm), number of columns (default 2).

## Process

### Step 1: Collect content

Use `wiki-query` to retrieve relevant wiki pages for the topic. Gather:
- Core definitions and formulas
- Intuitive explanations and analogies
- Concrete examples (architectures, applications)
- Common misconceptions worth addressing

### Step 2: Make an educational plan

Plan the card structure. Prioritize:
- **Motivation first** — why does this concept exist? What problem does it solve?
- **Build up gradually** — each card should be prerequisite-aware
- **Insight over enumeration** — every non-trivial card needs a 💡 lightbulb remark (see below)
- **Visual support** — use diagrams and equations where they clarify, not decorate
- **No cognitive overload** — fewer cards with depth beats many shallow cards

### Step 3: Generate the poster HTML

Use the template at `${CLAUDE_SKILL_DIR}/template.html` as a starting point.

**Architecture:**
- `CARD_REGISTRY` — defines each card's content (title, color, JSX body)
- `DEFAULT_LAYOUT` — defines column structure and card ordering
- React state manages layout, with localStorage persistence
- `window.posterAPI` exposes functions for programmatic control

**Key things to customize:**
1. Update `CARD_REGISTRY` with the topic's content (each section is a card)
2. Update `DEFAULT_LAYOUT` with column assignments
3. Update `DEFAULT_FONT_SCALE` (start at 1.3, user can adjust with A-/A+ buttons)
4. Update the header (title, subtitle, source reference — see header pattern below)
5. Update `@page { size: WIDTHmm HEIGHTmm; }` and `body { width: WIDTHmm; height: HEIGHTmm; }` for the poster dimensions

---

## Card content patterns

All text sizes use `calc(Xpt * var(--font-scale))` so A-/A+ works globally.

### 💡 Lightbulb remark (use generously — default to including)

**This is the primary teaching mechanism.** Whenever a card contains a concept, formula, or diagram that has a non-obvious "why", end it with a lightbulb remark. Most cards benefit from one. Skip only when the card is purely visual (a figure that speaks for itself) or a pure reference table with no interpretive content. Use the card's accent color for the emoji.

```jsx
<p style={{fontSize:'calc(7pt * var(--font-scale))',color:'var(--text-light)',
           borderTop:'0.3mm solid #eee',paddingTop:'1mm',marginTop:'0.5mm'}}>
  <span style={{color:'var(--ACCENT)'}}>💡</span> <b>Key insight:</b> Explanation of WHY this matters — the non-obvious implication, the hidden invariant, or the intuition that makes the formula click.
</p>
```

The separator line (`borderTop`) is part of the pattern — it visually separates the remark from the card body.

### Highlight box (`.hl`)

For the single most important takeaway at the top of a card:
```jsx
<div className="hl">         {/* blue by default */}
  <p>Key statement in <b>bold</b>.</p>
</div>
<div className="hl orange">  {/* or: teal, purple */}
  <p>Variant highlight.</p>
</div>
```

### Equation (`.eq`)

```jsx
<div className="eq">{'$$\\text{Attention}(Q,K,V)=\\text{softmax}\\!\\left(\\frac{QK^\\top}{\\sqrt{d_k}}\\right)V$$'}</div>
<div className="eq teal">{'$$x_{t+1} = x_t - \\eta \\nabla f(x_t)$$'}</div>
{/* colors: default=orange, teal, blue, purple */}
```
Escape backslashes in JSX strings: `\\frac` not `\frac`.

### Numbered steps (`.steps`)

```jsx
<div className="steps">
  <div className="step">
    <div className="step-num">1</div>
    <p><b>Step name:</b> What happens here.</p>
  </div>
  <div className="step">
    <div className="step-num">2</div>
    <p><b>Step name:</b> What happens here.</p>
  </div>
</div>
```

### Role boxes (`.roles`) — compare N concepts side-by-side

```jsx
<div className="roles">
  <div className="role-box q">
    <div className="rl">Q — Query</div>
    <p><i>"What am I looking for?"</i></p>
  </div>
  <div className="role-box k">
    <div className="rl">K — Key</div>
    <p><i>"What do I offer?"</i></p>
  </div>
  <div className="role-box v">
    <div className="rl">V — Value</div>
    <p><i>"My actual content."</i></p>
  </div>
</div>
{/* role-box colors: q=blue, k=orange, v=teal */}
```

### Table

```jsx
<table>
  <thead><tr><th>Method</th><th>Cost</th><th>Parallel?</th></tr></thead>
  <tbody>
    <tr><td>RNN</td><td>{'$O(n \\cdot d^2)$'}</td><td>No</td></tr>
    <tr><td><b>Self-Attn</b></td><td className="best">{'$O(n^2 \\cdot d)$'}</td><td className="best">Yes</td></tr>
  </tbody>
</table>
{/* className="best" renders in teal bold — use for the winning cell */}
```

### CSS flow diagram (`.flow-box`) — PREFERRED over SVG for architectures

Use CSS flow boxes for linear/block-structured architecture diagrams. This is **much faster to author and tweak** than SVG:

```jsx
<div style={{display:'flex',alignItems:'stretch',gap:'1mm',width:'100%',fontSize:'calc(6.5pt * var(--font-scale))'}}>
  <div className="flow-box input-box" style={{flex:1,padding:'4mm 2mm',display:'flex',alignItems:'center',justifyContent:'center',textAlign:'center'}}>Input</div>
  <span style={{display:'flex',alignItems:'center',color:'var(--text-light)',fontWeight:800}}>→</span>
  <div className="flow-box attn-box" style={{flex:2,padding:'4mm 2mm',display:'flex',alignItems:'center',justifyContent:'center',textAlign:'center'}}>Self-Attn</div>
  <span style={{display:'flex',alignItems:'center',color:'var(--text-light)',fontWeight:800}}>→</span>
  <div className="flow-box norm-box" style={{flex:1.5,padding:'4mm 2mm',display:'flex',alignItems:'center',justifyContent:'center',textAlign:'center'}}>LayerNorm</div>
  <span style={{display:'flex',alignItems:'center',color:'var(--text-light)',fontWeight:800}}>→</span>
  <div className="flow-box out-box" style={{flex:1,padding:'4mm 2mm',display:'flex',alignItems:'center',justifyContent:'center',textAlign:'center'}}>Output</div>
</div>
```

Available `.flow-box` variants:
- `input-box` — dashed gray border (inputs/inputs)
- `attn-box` — blue (self-attention)
- `mattn-box` — red (masked attention)
- `xattn-box` — purple (cross-attention)
- `ffn-box` — orange (feed-forward)
- `norm-box` — light gray (normalization, residual)
- `out-box` — dashed green (outputs)

### SVG diagrams — use sparingly

**Only use SVG when CSS layout cannot express the structure** — e.g., non-linear arrow routing, crossing connections, spatial/geometric diagrams (pixel grids, point clouds). SVG requires precise pixel coordinates and is slow to iterate. Budget extra time if used; do not attempt to modify existing SVG schemas unless strictly necessary.

### IO row (`.io-row`) — before/after or input/output comparison

```jsx
<div className="io-row">
  <div className="io-box blue-box">
    <div className="io-label">Input</div>
    <p>Description</p>
  </div>
  <div className="io-arrow">→</div>
  <div className="io-box teal-box">
    <div className="io-label">Output</div>
    <p>Description</p>
  </div>
</div>
{/* io-box variants: blue-box, teal-box */}
```

### Figure card

```jsx
<div className="fig">
  <div className="fig-wrap"><img src="file.png" alt="description" /></div>
  <div className="cap"><b>Caption title.</b> Description.</div>
</div>
```

Images MUST use `width:100%; height:100%; object-fit:contain` (already in `.fig-wrap img` CSS). Each column must always have one `grow: true` card to fill remaining space — the `isCardGrow()` function handles this automatically.

---

## Header pattern (educational poster)

For self-educational posters, the header is simple — no logos, no QR codes:

```jsx
<div className="header">
  <div className="header-left">
    <h1>Topic Title</h1>
    <div className="sub">Subtitle — what aspect is explained</div>
    <div className="authors">Based on Author (Year) &amp; Author et al. (Year)</div>
    <div className="aff">source-url-1 · source-url-2</div>
  </div>
</div>
```

---

## Color system

Five accent colors, each with a light variant for backgrounds:

| Name   | Variable        | Light bg           | Use for                  |
|--------|-----------------|--------------------|--------------------------|
| blue   | `--blue`        | `--blue-light`     | default, primary concept |
| orange | `--orange`      | `--orange-light`   | secondary, equations     |
| teal   | `--teal`        | `--teal-light`     | derived concepts, output |
| purple | `--purple`      | `--purple-light`   | cross-connections        |
| red    | `--red`         | `--red-light`      | masked/causal elements   |

Card colors: add class `orange`, `teal`, `purple`, or `red` to `.card`. The heading and borders auto-match.

---

## Layout optimization

After generating, use Playwright to measure whitespace and bake optimal dimensions into the defaults:

```python
from playwright.sync_api import sync_playwright
import os

with sync_playwright() as p:
    browser = p.chromium.launch()
    page = browser.new_page(viewport={'width': 3200, 'height': 2260})
    page.goto('file://' + os.path.abspath('poster/index.html'))
    page.wait_for_load_state('networkidle')
    page.wait_for_timeout(3000)

    waste = page.evaluate('window.posterAPI.getWaste()')
    layout = page.evaluate('window.posterAPI.getLayout()')
    print(waste, layout)

    # Try different column widths
    for w in range(160, 320, 10):
        page.evaluate(f'window.posterAPI.setColumnWidth("col1", {w})')
        page.wait_for_timeout(30)
        wst = page.evaluate('window.posterAPI.getWaste().total')
        print(w, wst)

    page.screenshot(path='/tmp/poster_check.png')
    browser.close()
```

Read `/tmp/poster_check.png` to visually inspect. Then bake best values into `DEFAULT_LAYOUT`, `DEFAULT_CARD_HEIGHTS`, `DEFAULT_FONT_SCALE`.

---

## Important guidelines

- **💡 Lightbulb + separator on every non-trivial card.** This is the primary teaching mechanism. Never skip it.
- **CSS flow boxes before SVG.** Always try CSS architecture diagrams first. Reserve SVG for spatial diagrams that truly cannot be expressed otherwise.
- **No blank space.** Use aspect-ratio-aware column assignment, `width:100%; height:100%; object-fit:contain` on images, and auto-grow cards.
- **Insight over enumeration.** This is a learning poster, not a summary. The lightbulb remarks carry the understanding.
- **Font scaling.** All text sizes use `calc(Xpt * var(--font-scale))`. Start with `--font-scale: 1.3`.
- **Print-optimized CSS.** `@media print` hides all edit UI and sets `transform: none !important`. `@page` sets exact dimensions.
- **Self-contained.** No build step, no npm, no server. Single HTML file with CDN dependencies. Works as `file://`.
- **Equations.** Use KaTeX (CDN). Escape backslashes in JSX: `{'$\\mathcal{L}$'}`.

---

## Step 4: Open and iterate

Open the poster in the browser and explain the editing controls:
- **Preview** — toggle edit UI off to see print layout
- **A-/A+** — adjust font size globally
- **Drag column dividers** (vertical blue bars) — resize columns
- **Drag row dividers** (horizontal blue bars) — resize cards within columns
- **Click-to-swap** — click a card's diamond handle, then another's to swap
- **Move/insert** — click a card's handle, then click a dashed orange drop zone
- **Save** — downloads `poster-config.json`
- **Copy Config** — copies layout JSON to clipboard
- **Reset** — restore defaults

When the user pastes a config JSON, update `DEFAULT_LAYOUT`, `DEFAULT_CARD_HEIGHTS`, `DEFAULT_FONT_SCALE` in the HTML to match, and write it to `poster-config.json`.

---

## Programmatic API (window.posterAPI)

- `swapCards(id1, id2)` — swap two cards
- `moveCard(cardId, targetColId, position)` — move a card
- `setColumnWidth(colId, widthMm)` — set column width (null for flex)
- `setCardHeight(cardId, heightMm)` — set explicit card height
- `setFontScale(scale)` — set global font scale
- `getWaste()` — measure whitespace in figure containers
- `getLayout()` — get rendered layout dimensions
- `getConfig()` — get serializable config
- `resetLayout()` — restore defaults
- `saveConfig()` — download poster-config.json
- `copyConfig()` — copy config to clipboard
