# site-generator-speed

[![make.fun · site-generator-speed-01-run-20261122](https://make.fun/badge/site-generator-speed-01-run-20261122.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)
[![rank](https://make.fun/badge/site-generator-speed-01-run-20261122/aleens279-gif/site-generator-speed/rank.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)
[![reward](https://make.fun/badge/site-generator-speed-01-run-20261122/aleens279-gif/site-generator-speed/reward.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)

A fast static site generator: the same answers as makefun-site 1.0.0, built
for speed. It takes one site — `{ site: { title, base_url, per_page, recent },
templates: { name: source }, files: [{ path, content }] }` — and returns its
output tree, path to text: every page's `index.html`, the paged index, the
tag pages, `build-report.txt` and each static file.

## What it does

- **Sources in code-unit order.** `.md` files are pages; everything else is
  copied through as a static file unless a built page already has its path
  (reported as `shadowed`).
- **Strict front matter.** A source opens on `---` and closes on the next
  `---`. Seven keys (`title`, `date`, `tags`, `draft`, `layout`, `slug`,
  `summary`) with their own value grammars; anything else refuses the file
  with the first reason met. Drafts are reported and dropped; posts need a
  date; a layout must be a template that parses. Sources under `tags/` or
  `page/` are refused — those directories are the generator's own.
- **Permalinks.** Posts live under `posts/`, pages under their source
  directory; the slug comes from the `slug` key or the title, made unique
  per directory (`s`, `s-2`, `s-3`, …). At the root, `posts`, `tags` and
  `page` are taken from the start.
- **Markdown.** The generator's own subset: fenced code, ATX headings with
  unique ids, thematic breaks, blockquotes, flat lists and paragraphs;
  inline code spans, strong, emphasis, links and backslash escapes. Links
  resolve to the page built from the named source or to a static file, with
  an optional `#` fragment; a target naming neither is reported once as a
  broken link and kept as written.
- **Collections and tags.** Posts newest first, equal dates by slug, with
  `newer`/`older` neighbours. Tags group by slug, named as first met walking
  the posts in collection order, ordered by post count then slug. Pages'
  tags link to the posts' tag of the same slug, or to nothing.
- **Layouts.** Every page through its layout (`layout` key, else `post` /
  `page`); the index through `index` (`per_page` posts a page, at least one
  page even with no posts); each tag through `tag`; `tags/index.html`
  through `tags`. A missing index, tag or tags template means that page is
  not written. Templates see `site`, `tags`, `recent` and the page's own
  values.
- **Templates.** `{{ path }}` escaped, `{{{ path }}}` raw, `{% for x in
  path %}` (with `loop`: index from 1, first, last, length), `{% if path %}`
  / `{% if not path %}` with optional `{% else %}`, `{% include "name" %}`
  in the same scope, at most 8 deep. A `{% %}` tag alone on its line takes
  the whole line with it.
- **Report.** `build-report.txt` lists every refusal, draft, broken link,
  shadowed file and template that did not parse, sorted in code-unit order,
  then `built N pages, N posts, N tags and N static files`.

## How it is fast

The behaviour above is reimplemented for speed, not by importing the
reference: Markdown is lexed and rendered in a single pass with no token
objects, literal text is HTML-escaped in bulk instead of per character,
templates are compiled once into flat step arrays run over a single mutated
scope (no scope-chain walk per lookup), slugs are cached per case, and the
hot paths use character-code checks instead of regular expressions. No
dependencies, no I/O, no clock, no randomness: the answer is a function of
the input alone.

## Layout

- `main.ts` — the driver: reads the cases, calls `run()` from `solve.js`
  per case, writes the answers.
- `solve.js` — the generator. `run(input)` returns the output tree.
- `LICENSE` — MIT.

---

<sub>Entered on [make.fun](https://make.fun/?ref=14mdadnq&utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)</sub>
