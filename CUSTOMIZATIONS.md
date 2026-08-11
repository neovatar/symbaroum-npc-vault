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

## Design decisions worth remembering

### Light/dark colors are dsnvs's, deliberately swapped

- **File:** `quartz.config.yaml`, `configuration.theme.colors`
- **What:** `lightMode` uses the bg/text/etc. values from
  [dsnvs](https://neovatar.github.io/dsnvs/)'s *dark* mode, and `darkMode`
  uses dsnvs's *light* mode values. This is intentional, not a mistake —
  when the viewer's system is in light mode, the site renders with dsnvs's
  dark palette, and vice versa.
- **Why:** Explicit user choice (asked and confirmed) when fixing a
  pink/brown color artifact in the graph view.
- **Also:** `tertiary` is set equal to `secondary` (`#268bd2`, dsnvs's one
  link-blue accent) in both modes. It was previously an invented orange
  (`#ff6600`, pulled from an unrelated syntax-highlighting class on
  dsnvs) — the graph's `quartz-community/graph` plugin uses `--tertiary`
  for visited-node fill and tag-node outlines, and a small orange
  shape/outline read as pink (light mode) or muddy brown (dark mode).
  dsnvs doesn't actually have a second accent color of its own, so if you
  want a distinct `tertiary` again, it'll need to be chosen fresh rather
  than pulled from dsnvs.

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
