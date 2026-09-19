# Repository purpose

This is a personal Obsidian wiki (Mathematics, Tech, AI, History, and other domains), maintained with LLM assistance. It is not a software project — there is no build, no tests, no dependencies.

- `SCHEMA.md` — the wiki's conventions: page frontmatter, `[[slug]]` cross-references, citation rules, log format. Read it before writing or editing any wiki content.
- `wiki/index.md`, `wiki/overview.md`, `wiki/log.md`, `wiki/pages/*.md` — the wiki itself.
- `raw/` — immutable source documents. Never modify files here.
- `assets/` — downloaded images, PDFs, attachments.
- `poster/` — generated teaching posters (HTML), one subfolder per poster.

## Workflows

Structured workflows for common operations live in `.github/skills/<name>/SKILL.md`. Copilot loads a skill automatically when the user's request matches its description — no slash command needed, just ask naturally:

- `wiki-init` — bootstrap a new wiki
- `wiki-ingest` — add a new source (paper, article, URL, transcript, file, or code)
- `wiki-query` — ask a question against the wiki, grounded in its pages
- `wiki-update` — revise existing pages when knowledge changes
- `wiki-lint` — audit the wiki for broken links, orphan pages, contradictions, staleness
- `wiki-audit` — fact-check a single page's footnotes against its cited sources
- `make-poster` — generate an HTML teaching poster from the wiki knowledge base

## Conventions to always apply

- Cross-reference wiki pages with `[[slug]]` (Obsidian wikilink), never markdown links, for anything inside `wiki/pages/`.
- Every non-common-knowledge factual claim needs a footnote citation — see the **Citations** section in `SCHEMA.md` for the exact format.
- `wiki/log.md` is append-only. `wiki/pages/` is flat — no subdirectories.
- Never answer a wiki question from general knowledge alone — read the relevant pages first (see wiki-query).
