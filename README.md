# Raptacon Brand Kit

Brand assets for FRC Team 3200 (Raptacon Robotics): logo, wordmark, brand color, and
fonts. This repo is meant to be pulled into other Raptacon repos as a git submodule
(e.g. the recruitment flyer references `brand-kit/logo/raptor-mark.png` and
`brand-kit/fonts/ethnocentric/…` directly) — keep that in mind before renaming or
moving files, since paths here are used elsewhere.

## Brand color

**Raptacon Green**

| Format | Value |
|---|---|
| Hex | `#84EC5C` |
| RGB | `132, 236, 92` |
| RGB (hex bytes) | `84 EC 5C` |

## What's in here

```
brand-kit/
  logo/
    raptor-mark.svg         raptor mark only (no text)
    raptor-mark.png         raptor mark, rasterized, transparent background
    lockup-horizontal.svg   raptor mark + "RAPTACON 3200" side by side
    lockup-horizontal.png   transparent background
    lockup-square.svg       raptor mark + "RAPTACON 3200" stacked, square canvas
    lockup-square.png       transparent background
    *-dark.svg / *-dark.png same three logos in white (#FFFFFF), for dark backgrounds
                             (raptor-mark-dark, lockup-horizontal-dark, lockup-square-dark)
    *-auto.svg              same three logos, black or white depending on the
                             viewer's light/dark setting (prefers-color-scheme)
  fonts/
    raptacon.sfd            custom "Raptacon" display font — editable source
    raptacon.ttf            custom "Raptacon" display font — installable
    glyphs/                 standalone per-letter SVGs (A, C, N, P, R, T)
                             for hand-editing individual letterforms
    ethnocentric/
      Ethnocentric-Regular.otf       licensed display font used for the wordmark text
      Typodermic Desktop EULA 2026.pdf
```

All logo SVGs are pure vector: straight lines and true circular arcs only, no traced
bitmap noise, no embedded fonts or raster images. The originals are black-filled (`#000000`) for light
backgrounds; the `-dark` versions are white for dark ones (see below). Recolor the `fill`
attribute (e.g. to `#84EC5C`) for a colored version, or drop them onto a solid
brand-green background for the reversed look.

## Light and dark backgrounds

The base files are black ink for light backgrounds. Each has a `-dark` twin with the
same geometry and pixel size, filled white (`#FFFFFF`), for dark backgrounds such as
`#0f1720` or `#1b2430`. Raptacon Green (`#84EC5C`) also reads well on those (contrast
12:1 on `#0f1720`) if you recolor a logo green.

To follow the device setting in HTML:

```html
<picture>
  <source srcset="brand-kit/logo/lockup-horizontal-dark.svg"
          media="(prefers-color-scheme: dark)">
  <img src="brand-kit/logo/lockup-horizontal.svg" alt="Raptacon 3200">
</picture>
```

`logo/*-auto.svg` does the same switch inside one file (an embedded `<style>` with
`@media (prefers-color-scheme: dark)`), so a plain `<img src="…-auto.svg">` works.

Both of these follow the OS/browser setting only. A site with its own manual theme
toggle should pick the file from its theme state instead, e.g. switch the `<img>`
`src` between the base and `-dark` file, or render both and hide one with CSS keyed
on the theme attribute:

```css
.logo-dark { display: none; }
[data-theme="dark"] .logo-light { display: none; }
[data-theme="dark"] .logo-dark { display: inline; }
```

## Using the assets

Drop the SVGs into anything that reads vector graphics, or use the pre-rendered
transparent PNGs directly. To render a PNG at a different resolution from an SVG:

```
magick -background none -density 600 logo/lockup-square.svg out.png
```

(`-density` is roughly "pixels per inch" for the SVG's own units; raise it for a
larger/sharper PNG, e.g. `-density 1200` for print-resolution output. Swap
`-background none` for `-background white` if you want a flat background baked in
instead of transparency.)

To use the Ethnocentric font on a machine (for design tools, not for redistributing
the font file itself — see License below): double-click
`fonts/ethnocentric/Ethnocentric-Regular.otf` and install it, or drop it into your
OS/font-manager's font folder.

## Using this as a submodule from another repo

```
git submodule add https://github.com/Raptacon/brand-kit.git brand-kit
git submodule update --init --recursive   # after cloning a repo that already has it
```

Then reference files by their path inside the submodule, e.g. in LaTeX:
`\includegraphics{brand-kit/logo/raptor-mark.png}`, or load
`brand-kit/fonts/ethnocentric/Ethnocentric-Regular.otf` directly via `fontspec`'s
`Path=` option — no need to install the font on the build machine.

## Modifying the logo

Every logo SVG is hand-authored and editable directly in Inkscape (or any SVG editor).

- `raptor-mark.svg` — the raptor mark. One `<g>` of straight-line/arc `<path>`s. Left/right
  mirror-symmetric except the intentionally turned head.
- `lockup-horizontal.svg` / `lockup-square.svg` — composited lockups. Each is a
  top-level SVG containing two nested `<svg x="…" y="…" width="…" height="…"
  viewBox="…">` blocks, one holding the raptor mark's paths and one holding the
  wordmark's paths (see "Modifying the font" below for where the wordmark paths come
  from). To change the gap/overlap between the raptor and the text, or their relative
  sizes, adjust the `x`/`y`/`width`/`height` on those two nested `<svg>` elements —
  the artwork inside doesn't need to change.

## Modifying the font

`fonts/raptacon.sfd` is the editable FontForge project for the custom "Raptacon"
display font (currently: R, A, P, T, C, O, N, and digits 0/2/3; the rest of the
alphabet is not yet designed — the logo lockups use the licensed Ethnocentric font for
their text, not this one). Open it directly in the FontForge GUI to edit glyphs:

```
fontforge fonts/raptacon.sfd
```

For letters that benefit from freehand tweaking, `fonts/glyphs/*.svg` are the same
letterforms exported as standalone single-path SVGs — edit one in Inkscape, then
re-import it into the glyph in FontForge (Element → this glyph, or via script with
`glyph.importOutlines("path/to/Letter.svg")`).

After editing, regenerate the installable TTF from the SFD:

```
fontforge -lang=ff -c 'Open("fonts/raptacon.sfd"); Generate("fonts/raptacon.ttf")'
```

Font geometry conventions used throughout (useful context if hand-editing paths):
cap height 15 units in the authoring grid, scaled ×60 into the font (cap height 900
of a 1000-unit em); a 45° chamfer (~2.2 units in the authoring grid) is the recurring
corner treatment; stroke width is 5 units in the authoring grid. Letters are built as
single closed outlines (no separate hole/counter subpaths) using a "slit" technique
where a counter connects back to the outer boundary through a zero-width bridge —
FontForge's `removeOverlap()` / `correctDirection()` clean up unions when a glyph is
assembled from multiple overlapping contours instead.

## Tool install (macOS, via Homebrew)

```
brew install fontforge imagemagick
```

Inkscape (optional, only needed for GUI hand-editing of SVGs — everything here can
also be edited as raw SVG/XML by hand or regenerated by script):

```
brew install --cask inkscape
```

## License

- **Raptor mark, wordmark, and the custom "Raptacon" font**: original work for Team
  3200, free to use and modify for team purposes.
- **Ethnocentric** (`fonts/ethnocentric/Ethnocentric-Regular.otf`, by Typodermic
  Fonts / Ray Larabie): free for personal and commercial use in **static, fixed**
  output — logos, wordmarks, print, merchandise, exported images/video. Do **not**
  redistribute the font file itself (e.g. don't publish it on a public website for
  download, embed it as a web font, or bundle it into a product), and don't use it
  to train/fine-tune an AI model. Full terms in the included EULA PDF; if anything
  here conflicts with that PDF, the PDF governs.
