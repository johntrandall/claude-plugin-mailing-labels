# mailing-labels: a Claude Code plugin

Two skills that get labels and codes onto paper from a Mac:

| Skill | What it does |
|---|---|
| `half-sheet-label` | Prints a shipping or return label PDF (USPS, UPS, Pirate Ship, Amazon returns...) onto peel-and-stick half-sheet sticker stock (Avery 8126/5126), barcode at 100%, through the [`half-sheet-label`](https://github.com/johntrandall/half-sheet-label) CLI. |
| `print-qr` | Prints a QR code or image centred on a Letter page with an optional caption, through ImageMagick and `lp`. |

## Install

```
/plugin marketplace add johntrandall/claude-plugins
/plugin install mailing-labels@johntrandall-plugins
```

## Requirements

- macOS with CUPS printing (`lp`), and a printer.
- For `half-sheet-label`: `brew install johntrandall/tap/half-sheet-label`, Avery 8126/5126
  (or equivalent) half-sheet label stock, and a config file naming your printer and label tray
  (the skill walks Claude through it).
- For `print-qr`: ImageMagick 7 (`brew install imagemagick`) for PNG/SVG sources.

## Licence

MIT, see [LICENSE](LICENSE).
