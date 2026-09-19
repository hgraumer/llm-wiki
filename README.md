Based on 

- the general idea in: https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f
- the skills from: https://github.com/kfchou/wiki-skills/tree/main

## Usage

Workflows are GitHub Copilot Skills under `.github/skills/<name>/SKILL.md`. Copilot picks the right one automatically from what you ask — just describe what you want, no slash command needed.

- **Add a source and integrate it into the wiki:** ask to ingest a file/URL/text (wiki-ingest) — it reads the source, writes a summary page, updates related entity/concept pages, backlinks, `index.md`, `overview.md`, and `log.md`.
- **Ask a question:** just ask (wiki-query) — answers grounded in wiki pages, with citations, offers to save the answer.
- **Update existing knowledge:** ask to update a page (wiki-update) — revises pages when something changes, shows diffs, logs the reason and source.
- **Health check:** ask to lint/audit the wiki (wiki-lint) — finds broken links, orphan pages, contradictions, stale claims.
- **Fact-check a page:** ask to fact-check a page (wiki-audit) — verifies every footnote against its cited source.
- **New wiki:** ask to bootstrap a new wiki (wiki-init) — creates `SCHEMA.md`, `raw/`, `wiki/`, `assets/` at a chosen path.
- **Teaching poster:** ask for a poster on a topic (make-poster) — generates a printable HTML poster from the wiki content.
