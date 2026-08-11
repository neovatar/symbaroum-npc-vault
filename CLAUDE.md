# CLAUDE.md

Guidance for Claude Code when working in this repo.

## What this is

An Obsidian vault (`content/`) tracking NPCs/Locations/Factions for a
Symbaroum campaign, built into a static site with Quartz v5 (vendored at
repo root under `quartz/`) and deployed to GitHub Pages on push to `main`
via `.github/workflows/deploy.yml`. Full details: `README.md`.

## Before touching `quartz/` or `quartz.config.yaml`

**Read [`CUSTOMIZATIONS.md`](./CUSTOMIZATIONS.md) first.** It tracks local
edits to the vendored Quartz engine (things that can be silently reverted
by `npx quartz upgrade`) and non-obvious config gotchas discovered the
hard way. If you make another edit of either kind — an engine-source
change, or a config setting whose necessity isn't obvious from reading
`quartz.config.yaml` alone — add it there too, in the same format, so the
next session doesn't have to rediscover it.

## Key conventions (see README.md for the full explanation)

- Notes need `publish: true` in frontmatter to appear on the site
  (`explicit-publish` plugin, fail-closed).
- Location/faction are recorded both as frontmatter properties and as
  mirrored nested tags (`#faction/x` — rendered without the `#`, see
  `CUSTOMIZATIONS.md`).
- GM-only content uses `publish: false` + a `Name (GM).md` companion note,
  never inline in a published note. **This repo is public** — that flag
  only controls the built site, not GitHub's raw file view.
- `content/Templates/` and `content/.obsidian/` are excluded from the
  build via `ignorePatterns`, not via `publish`.

## Verifying changes

`npx quartz build` locally, then check the relevant files in `public/`
(or `npx quartz build --serve` to preview). For anything touching
fonts/colors/CSS, grep the built `public/*.css` for the expected values
rather than trusting the config alone — see the quartz-fonts gotcha in
`CUSTOMIZATIONS.md` for why that matters here specifically.
