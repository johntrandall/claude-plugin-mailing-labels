---
name: print-qr
type: procedure
description: "Print a QR code or other image (PNG/SVG/PDF) to a paper printer, centred on a Letter page, with an optional one-line caption underneath. Auto-invoke when an agent has a drop-off QR code, a Wi-Fi QR, a calibration target, or any image-as-document that needs to land on plain paper. NOT for purchased shipping/return label PDFs that will be stuck to a box: those go to half-sheet-label (sticker stock)."
tags: [printing, qr, returns, cups]
freshness:
  last_refreshed_at: 2026-10-15
  auto_refresh_cadence: quarterly
---

# Print QR: an image on plain paper, with a caption

Feeds a finished PNG, SVG or PDF to CUPS, centred on a Letter page, with an optional caption
under it. For a label that gets stuck to a package, use `half-sheet-label` instead.

**Requires:** macOS or Linux with CUPS (`lp`), and ImageMagick 7 (`magick`;
`brew install imagemagick`) for PNG/SVG sources. Check with `magick --version`; on ImageMagick 6
the command is `convert`.

**Printer:** `lp` without `-d` prints to the system default printer (`lpstat -d`). Add
`-d <queue>` (see `lpstat -p`) when the user names a printer.

## PNG or SVG, with a caption

```bash
src="$1"          # e.g. ~/Downloads/return-RYZ123.png
caption="$2"      # e.g. "Return RYZ123 - drop at UPS"
tmpdir=$(mktemp -d)
page="$tmpdir/page.pdf"

# 2550x3300 px = Letter at 300 dpi; caption at about 10 pt printed
magick -size 2550x3300 xc:white \
  \( "$src" -resize 1500x1500 \) -gravity center -composite \
  -gravity south -font Helvetica -pointsize 36 -fill black \
    -annotate +0+400 "$caption" \
  "$page"

lp -o media=Letter -o fit-to-page "$page"     # add -d <queue> for a specific printer
rm -rf "$tmpdir"
```

Without a caption, drop the `-annotate` line. For A4, use `-size 2480x3508` and `media=A4`.
To preview instead of printing, run the `magick` step and `open "$page"`.

## PDF source

No rendering needed:

```bash
lp -o media=Letter -o fit-to-page "$pdf_path"
```

## When not to use this skill

- **A label that gets stuck to a package**: `half-sheet-label` (this plugin).
- **A multi-page document**: `lp` it directly.

## Calling convention from other skills

```
Hand-off to print-qr with:
  source: <path to PNG/SVG/PDF on the host>
  caption: <optional one-liner>
  printer: <CUPS queue, omit for the system default>
```

The source path must be on the host where `lp` runs, not inside a container.
