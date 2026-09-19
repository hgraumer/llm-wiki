Based on 

- the general idea in: https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f
- the skills from: https://github.com/kfchou/wiki-skills/tree/main

## Usage

Workflows are Copilot Chat prompt files under `.github/prompts/`, invoked as `/<name>`.

- **Add a source and integrate it into the wiki:** drop the file/URL/text on `/wiki-ingest` — it reads the source, writes a summary page, updates related entity/concept pages, backlinks, `index.md`, `overview.md`, and `log.md`.
- **Ask a question:** `/wiki-query` — answers grounded in wiki pages, with citations, offers to save the answer.
- **Update existing knowledge:** `/wiki-update` — revises pages when something changes, shows diffs, logs the reason and source.
- **Health check:** `/wiki-lint` — finds broken links, orphan pages, contradictions, stale claims.
- **Fact-check a page:** `/wiki-audit <page>` — verifies every footnote against its cited source.
- **New wiki:** `/wiki-init` — bootstraps `SCHEMA.md`, `raw/`, `wiki/`, `assets/` at a chosen path.
- **Teaching poster:** `/make-poster <topic>` — generates a printable HTML poster from the wiki content.
