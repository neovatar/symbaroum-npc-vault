# Symbaroum NPC Vault

An Obsidian vault tracking NPCs, locations, and factions for our Symbaroum
campaign, published as a static site (via [Quartz](https://quartz.jzhao.xyz))
with an interactive relationship graph.

**Live site:** https://neovatar.github.io/symbaroum-npc-vault

## ⚠️ This repo is public

The Markdown source in this repo — including any note with
`publish: false` — is publicly readable on GitHub, even though those notes
are excluded from the *built site*. **Do not put real spoilers, secrets, or
sensitive plot info in this repo.** Keep true GM secrets somewhere else
entirely (a private notes app, a private repo, etc.) and only put
player-safe summaries here, or content you're fine with players
technically being able to find if they go looking at the raw repo.

## Structure

- `content/NPCs/`, `content/Locations/`, `content/Factions/` — the actual
  vault content. Open `content/` as your Obsidian vault root.
- `content/Templates/` — note templates for new NPCs/Locations/Factions.
  Excluded from the build via `ignorePatterns` in `quartz.config.yaml`.
- `quartz.config.yaml` — site config (title, plugins, theme, graph/search
  settings).
- `quartz/` — the Quartz static site generator engine (vendored).

## Frontmatter conventions

Every note that should appear on the live site needs `publish: true` in
its frontmatter (see `content/Templates/`) — the
[`explicit-publish`](https://github.com/quartz-community/explicit-publish)
plugin only builds pages that opt in explicitly. Location/faction are
recorded both as frontmatter properties (for structured data) and as
nested tags like `#faction/iron-pact` (so Quartz's tag pages and Obsidian's
tag pane work natively).

Companion GM-notes files use `publish: false` and are named
`Name (GM).md`. See `content/Templates/NPC Template.md` and
`content/GM Index.md` for the convention — again, treat these as "hidden
from the built site," not "actually private," given this repo is public.

## Local development

```bash
npm ci
npx quartz build --serve
```

## Deployment

Pushing to `main` triggers `.github/workflows/deploy.yml`, which builds the
site and publishes it to GitHub Pages automatically.

## Customizations

See [`CUSTOMIZATIONS.md`](./CUSTOMIZATIONS.md) for local edits to the
vendored `quartz/` engine and non-obvious config decisions — check it
before/after running `npx quartz upgrade`.
