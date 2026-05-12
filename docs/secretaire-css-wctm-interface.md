# secretaire-css → wctm Interface Specification

This document describes exactly what wctm expects from secretaire-css output.
It is intended to guide implementation work in the secretaire project.

## Output files

secretaire-css generates one CSS file per modus variant.  wctm currently uses
two variants, each placed in the theme's asset directory:

| Variant | File path in wctm repo |
|---|---|
| `modus-vivendi-tinted` | `themes/sdowney-tailwind/assets/css/modus-vivendi-tinted.css` |
| `modus-operandi-tinted` | `themes/sdowney-tailwind/assets/css/modus-operandi-tinted.css` |

Nikola copies everything under `themes/sdowney-tailwind/assets/` to
`output/assets/` during build.  No further integration step is needed — drop
the file in and rebuild.

Only `modus-vivendi-tinted.css` is loaded by the templates today (see Load
Order below).  `modus-operandi-tinted.css` exists for future dark/light mode
switching.

## Load order and specificity

In the rendered page, stylesheets load in this order:

```
tailwind.css              ← Tailwind utilities (@layer utilities)
baguetteBox.min.css
modus-vivendi-tinted.css  ← secretaire-css output  ← HERE
tailwind-base.css         ← structural CSS (org-mode block layout etc.)
custom.css                ← site palette overrides (loaded last, wins)
```

Rules in `modus-vivendi-tinted.css` are **not** wrapped in any `@layer`, so
they beat all Tailwind utility classes by the CSS unlayered-beats-layered rule.
Keep all rules unlayered.

## Required CSS structure (v2)

The file must have two sections in order:

### 1. `:root` palette block

```css
/* secretaire-css: modus-vivendi-tinted
 * Generated: <ISO timestamp>
 */
:root {
    --bg-main:  #0d0e1c;
    --fg-main:  #ffffff;
    --fg-dim:   #e0e6f0;
    --red:      #ff8059;
    --green:    #44bc44;
    /* ... only palette entries referenced by the exported face rules ... */
}
```

**Minimum required variables** — wctm reads these directly:

| Variable | Role in wctm |
|---|---|
| `--bg-main` | Code block background (`--code-bg` in `custom.css`) |
| `--fg-main` | Code block foreground (`--code-fg` in `custom.css`) |

`custom.css` currently reads:

```css
--code-bg: var(--bg-main, #0d0e1c);
--code-fg: var(--fg-main, #ffffff);
```

The hardcoded fallbacks are the v1 values and keep the site working when the
`:root` block is absent (i.e. during any transition period).

Emit all other palette entries that are referenced by the `.org-*` face rules.
Only emit entries that are actually used — the design goal is a minimal,
self-documenting `:root`.

### 2. `.org-*` face rules

One rule per exported face, using `var(--palette-name)` for colors:

```css
/* font-lock-keyword-face: magenta-alt-other */
.org-keyword {
    color: var(--magenta-alt-other);
    font-weight: bold;
}

/* font-lock-string-face: green */
.org-string {
    color: var(--green);
}

/* font-lock-comment-face: fg-dim (italic) */
.org-comment {
    color: var(--fg-dim);
    font-style: italic;
}
```

For 4.x themes with a semantic mapping layer, annotate both levels:

```css
/* font-lock-keyword-face: keyword → magenta-alt-other → #b6a0ff */
.org-keyword {
    color: var(--magenta-alt-other);
    font-weight: bold;
}
```

## Face → CSS class name mapping

The class name is derived from the Emacs face symbol by:
1. Strip the `font-lock-` prefix
2. Strip the trailing `-face` suffix
3. Prepend `org-`

Examples (confirmed against v1 output):

| Emacs face | CSS class |
|---|---|
| `font-lock-keyword-face` | `.org-keyword` |
| `font-lock-string-face` | `.org-string` |
| `font-lock-comment-face` | `.org-comment` |
| `font-lock-comment-delimiter-face` | `.org-comment-delimiter` |
| `font-lock-type-face` | `.org-type` |
| `font-lock-builtin-face` | `.org-builtin` |
| `font-lock-function-name-face` | `.org-function-name` |
| `font-lock-function-call-face` | `.org-function-call` |
| `font-lock-doc-face` | `.org-doc` |
| `default` | `.org-default` (or handle via `:root` only) |

v2 **must produce the same short class names** as v1.  Any change to the class
name scheme breaks syntax highlighting for all existing org-mode posts.

## Face selection

Export all faces whose class names appear in the syntax-highlighted HTML that
Nikola's org-mode plugin produces.  The relevant face prefixes are:

- `font-lock-*` — core syntax highlighting
- `org-*` — org-mode structural faces (headings, links, keywords, etc.)
- `default` — needed for `--bg-main` / `--fg-main` extraction

Faces that inherit and add nothing new should be **omitted**.  The browser
handles CSS inheritance; an empty rule adds noise.

## v1 → v2 transition

v2 is a **drop-in replacement** for v1:
- Same file paths
- Same `.org-*` class names and values
- Adds the `:root` palette block at the top

`custom.css` already has fallback values for when `--bg-main` / `--fg-main`
are absent, so there is no breakage during transition.  The site looks
identical under v1 and v2; v2 just makes the palette single-sourced.

## What does NOT need to change in wctm

Nothing in the templates, Makefile, or Tailwind config changes.  Only the
generated modus CSS file changes.  The wctm side is ready.
