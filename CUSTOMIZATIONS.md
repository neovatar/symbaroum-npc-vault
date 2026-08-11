# Customizations

This file tracks local changes to the vendored Quartz engine (`quartz/`)
and non-obvious config decisions, so they can be re-applied or reconciled
after running `npx quartz upgrade` (which pulls updates from the upstream
`jackyzha0/quartz` repo and can overwrite/conflict with local edits to
`quartz/`).

Config-only changes (anything in `quartz.config.yaml`) aren't at risk from
upgrades — those are listed here only when the *reason* for a setting isn't
obvious from the setting itself.

## Engine-source edits (`quartz/`) — re-check after `quartz upgrade`

### Removed `#` prefix from tag links

- **File:** `quartz/styles/base.scss`
- **What:** Deleted the `&.tag-link { &::before { content: "#"; } }` rule
  (was nested under the `a` selector, right after `&:has(> img)`).
- **Why:** Preference — tags render as `location/thistle-hold` instead of
  `#location/thistle-hold`.
- **If it reappears after an upgrade:** find the `.tag-link` `::before`
  rule in `quartz/styles/base.scss` again and delete it.

### Custom overrides go in `custom.scss`, never `base.scss`

- **File:** `quartz/styles/custom.scss` (see the rule already there for a
  worked example)
- **What:** Quartz wraps `base.scss` in `@layer quartz-base` when it
  builds the CSS bundle (`componentResources.ts`). CSS `@layer` priority
  beats specificity *entirely* — a low-specificity rule in a
  higher-priority layer (e.g. `@quartz-themes/core`'s `obsidian-theme`
  layer) will always win over a rule in `quartz-base`, no matter how
  specific you make the `quartz-base` rule. `custom.scss`'s content is
  concatenated *outside* any `@layer` block, and unlayered CSS always
  beats layered CSS regardless of layer order — so any override that
  needs to reliably win belongs there, not in `base.scss`.
- **Real example this bit us:** `@quartz-themes/core` ships
  `body a.internal-link { background-color: rgb(from var(--highlight) r g
  b / 0.3) }` inside its `obsidian-theme` layer. Every tag pill and
  internal-link Quartz renders carries both the `.internal` and
  `.internal-link` classes, so this rule was silently overriding our
  intended `--highlight` background on every link/tag on the site — no
  amount of editing `quartz.config.yaml` colors touched it, because it
  was never the losing side of a specificity fight, it was in a lower
  layer entirely. Fixed with an unlayered override in `custom.scss`.
- **If colors/styles don't seem to apply and you're sure the config is
  right:** check the browser devtools "Styles" panel for which selector
  is actually winning. If it's coming from `@quartz-themes/core` or
  another plugin (not `quartz.config.yaml` or your own content), it's
  almost certainly a `@layer` priority issue — override it from
  `custom.scss`, not by tweaking specificity in `base.scss`.

## Design decisions worth remembering

### `tertiary` matches `secondary` (no second accent color)

- **File:** `quartz.config.yaml`, `configuration.theme.colors`
- **What:** `tertiary` is `#268bd2`, same as `secondary` — both modes.
- **Why:** dsnvs (the site this theme is matched to) only has one accent
  color (its link blue) in both light and dark mode, no second accent to
  pull from. An earlier attempt used an invented orange (`#ff6600`) for
  `tertiary`; the `@quartz-community/graph` plugin uses `--tertiary` for
  visited-node fill/tag-node outlines and the base stylesheet uses it for
  `a:hover` color, so that orange showed up as an unwanted
  pink/brown-reading tint sitewide on hover and in the graph. Fixed by
  making `tertiary` just reuse the blue.

## Config gotchas worth remembering

### Font must be set in *two* places

- **File:** `quartz.config.yaml`
- **What:** Both `configuration.theme.typography` *and* the
  `@quartz-community/quartz-fonts` plugin's own `options.header` /
  `options.body` / `options.code` need to match.
- **Why:** The `quartz-fonts` plugin generates the actual
  `--headerFont`/`--bodyFont`/`--codeFont` CSS variables independently of
  `configuration.theme.typography`, falling back to its own hardcoded
  defaults (Schibsted Grotesk/Source Sans Pro) when its own `options` are
  unset — regardless of what `theme.typography` says. If fonts look right
  on some pages but not others (or not at all), check both places agree.
