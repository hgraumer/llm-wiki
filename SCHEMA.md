# Wiki Schema

## Identity
- **Path:** C:\Users\hgrau\llm-wiki
- **Domain:** Personal knowledge base (Mathematics, Tech, AI, History and others)
- **Source types:** papers, articles, transcripts, parts of books, URLs
- **Created:** 2026-05-03

## Page Frontmatter
Every wiki page must start with:
```
---
title: <page title>
aliases:
  - <alternative name (optional — omit if none)>
tags:
  - tag1
  - tag2
sources:
  - source-slug1
updated: YYYY-MM-DD
---
```

## Cross-References
Use `[[slug]]` where slug = filename without `.md`.
Example: `[[transformer-architecture]]` → `wiki/pages/transformer-architecture.md`

These are native Obsidian wikilinks. Obsidian resolves them automatically across all subfolders in the vault.

## Obsidian

This wiki is an Obsidian vault. Set the vault root to `C:\Users\hgrau\llm-wiki`.

- `[[slug]]` wikilinks resolve automatically — Obsidian searches all subfolders
- `![[image.png]]` or `![[image.png|300]]` to embed images from `assets/`
- `![[doc.pdf#page=2]]` to embed a PDF page from `raw/`
- Tags defined in frontmatter appear in Obsidian's tag pane; nested tags use `parent/child` syntax
- `aliases` in frontmatter makes a page findable under alternative names in link suggestions

## Math (LaTeX)

Obsidian renders LaTeX natively. Use it for all mathematical notation — never write formulas as plain text.

Inline math (embedded in a sentence):
```
The complexity is $O(n \log n)$ in the average case.
```

Display math (standalone equation on its own line):
```
$$
\int_0^\infty e^{-x^2} \, dx = \frac{\sqrt{\pi}}{2}
$$
```

Multi-line derivations — use `align` inside display math:
```
$$
\begin{align}
f(x) &= x^2 + 2x + 1 \\
     &= (x + 1)^2
\end{align}
$$
```

Rules:
- Every mathematical symbol or formula in running prose uses `$...$`, not plain text
- Standalone definitions, theorems, and proofs use `$$...$$`
- Use `\text{...}` for words inside math mode

## Callouts

Use Obsidian callouts to surface structure inside pages. Keep them targeted — not every paragraph needs one.

```
> [!note] Key insight
> Reserve for the single most important takeaway from a section.

> [!warning] Caveat
> Limitations, common misunderstandings, or known failure modes.

> [!example] Example
> A concrete worked example or illustration.

> [!summary]- Proof sketch
> Use the collapsible form (trailing -) for long derivations or proofs.
```

Common types: `note`, `tip`, `warning`, `info`, `example`, `quote`, `summary`, `math`.

## Citations

Cite every non-common-knowledge factual claim. "Common knowledge" = uncontroversial,
undergraduate-level facts in this wiki's domain. Granularity is paragraph or claim,
never per-sentence. If you cannot produce a citation in one of the forms below,
find one, weaken the claim, or drop it.

Format: Markdown footnotes. Two citation kinds, three valid targets.

**Quote citation** (preferred):
```
The model uses 8 attention heads.[^1]

[^1]: [[attention-is-all-you-need]] §3.2.2 — "We employ h = 8 parallel attention layers"
```

**Synthesis citation** (when no single quote captures the claim):
```
The architecture is fundamentally an encoder-decoder with attention.[^2]

[^2]: [[attention-is-all-you-need]] §3.2-3.4 [synthesis] — encoder, decoder, and
      attention sections together describe the full multi-head architecture
```

Three rules for every footnote:

1. **The cited target is one of three forms:**
   - `[[source-slug]]` — a source-type wiki page (preferred for sources you've
     ingested via `wiki-ingest`)
   - `raw/<file>` or `assets/<file>` — a path to a local file (for drive-by
     citations where a synthesis page isn't worth creating)
   - `<URL>` — a live URL, tweet, or ephemeral source (no local copy required)

   Never cite entity, concept, or analysis pages — those are syntheses, not sources.

2. **A locator is present:** `§<section>`, `p.<n>`, `[HH:MM:SS]` for transcripts,
   URL anchor for web, or `(YYYY-MM-DD)` for dated posts.

3. **Either a verbatim quote, or the `[synthesis]` tag plus a description** of
   what the cited range supports. No third option.

**Drive-by citation examples:**
```
[^3]: raw/scaling-laws.pdf p.7 — "loss scales as a power law in compute"
[^4]: https://twitter.com/user/status/123 (2026-04-15) — "<tweet text>"
```

## Log Entry Format
```
## [YYYY-MM-DD] <operation> | <title>
```
Operations: init, ingest, query, update, lint, audit

## Index Categories
- Sources
- Entities
- Concepts
- Analyses

## Conventions
- raw/ is immutable — skills never modify it
- log.md is append-only — never rewritten, only appended
- index.md is updated on every operation that adds or changes pages
- All pages live flat in wiki/pages/ — no subdirectories
- overview.md reflects the current synthesis across all sources
