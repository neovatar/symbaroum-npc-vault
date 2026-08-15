# Symbaroum NPC Vault

An Obsidian vault tracking NPCs, locations, and factions for our Symbaroum
campaign, published as a static site (via [Quartz](https://quartz.jzhao.xyz))
with an interactive relationship graph.

**Live site:** https://neovatar.github.io/symbaroum-npc-vault

## ⚠️ This repo is public

The Markdown source in this repo — including any note with
`publish: false` — is publicly readable on GitHub, even though those notes
are excluded from the *built site*. This is one player's personal campaign
notes (in-character knowledge, theories, etc.), not GM material, but
**don't put anything here you wouldn't want other players in the group
stumbling across.**

## Structure

- `content/NPCs/`, `content/Locations/`, `content/Factions/`, `content/Houses/` — the actual
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
plugin only builds pages that opt in explicitly. Tags are simple, flat
category labels only (`npc`, `location`, `faction`, `house`) — no nested
tags. Connections between notes (an NPC's location/faction, etc.) go only
in frontmatter properties and `[[wikilinks]]`, never encoded as a tag.

## Portraits

NPC portrait images live in `content/NPCs/attachments/`, one file per NPC
(e.g. `elyn-thornwood.jpg`). Embed the portrait as the first line of the
note body, right after the frontmatter, using Obsidian embed syntax with a
width so it doesn't render huge:

```markdown
![[elyn-thornwood.jpg|200]]
```

No build config is needed — Quartz's `Assets` emitter copies every
non-Markdown file under `content/` verbatim, and the
`obsidian-flavored-markdown` plugin already resolves `![[...]]` embeds.

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
