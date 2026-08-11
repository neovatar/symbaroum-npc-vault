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

- **File:** `quartz/styles/custom.scss`
- **What:** Quartz wraps `base.scss` in `@layer quartz-base` when it
  builds the CSS bundle (`componentResources.ts`). CSS `@layer` priority
  beats specificity *entirely* — a low-specificity rule in a
  higher-priority layer (e.g. `@quartz-themes/core`'s `obsidian-theme`
  layer) will always win over a rule in `quartz-base`, no matter how
  specific you make the `quartz-base` rule. `custom.scss`'s content is
  concatenated *outside* any `@layer` block, and unlayered CSS always
  beats layered CSS regardless of layer order — so any override that
  needs to reliably win belongs there, not in `base.scss`.

### All theme color variables are re-pinned, unlayered, in `custom.scss`

- **File:** `quartz/styles/custom.scss`
- **What:** A `:root { --light: ...; --secondary: ...; ... }` /
  `:root[saved-theme="dark"] { ... }` block that duplicates
  `quartz.config.yaml`'s `configuration.theme.colors` values verbatim,
  emitted unlayered. **If you change the colors in `quartz.config.yaml`,
  you must also update this block by hand** — they aren't derived from
  each other.
- **Why:** `@quartz-themes/core`'s `obsidian-theme` layer redefines
  *every* one of Quartz's theme variables (`--secondary`, `--tertiary`,
  `--light`, `--highlight`, `--textHighlight`, ...) by bridging them
  through its own Obsidian-native variable names (`--text-accent`,
  `--color-base-00`, `--text-highlight-bg`, ...), so ported Obsidian
  themes can drive them. We use `theme: default` (no ported theme), so
  most of those bridges happen to resolve back to something close to our
  real colors via computed `hsl()`s — except `--highlight` and
  `--textHighlight`, which fall through to a hardcoded literal
  `rgba(255, 208, 0, 0.4)` (Obsidian's default text-highlighter yellow)
  with **no connection to `quartz.config.yaml` at all**. That's what was
  actually producing the "pink/brown" tag-pill/link backgrounds reported
  — not the `body a.internal-link` rule from the first attempt (fixing
  which selector rule such that background-color rule "wins" doesn't
  matter if the `--highlight` *variable itself* is hijacked to something
  else higher up — that's what actually happened here, and cost two
  rounds of "fixes" that changed nothing visible before it was found).
- **If colors/styles don't seem to apply and you're sure the config is
  right:** don't just check which *rule* wins in devtools — also check
  the **Computed** tab for the actual resolved value of the CSS
  *variable* itself (e.g. `--highlight`), since `@quartz-themes/core` can
  hijack the variable's value in a higher layer even when your own rule
  correctly wins the property fight. If a variable's computed value
  doesn't match `quartz.config.yaml`, add/update it in this `:root` /
  `:root[saved-theme="dark"]` block in `custom.scss`.

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
