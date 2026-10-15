---
name: half-sheet-label
type: procedure
description: "Print a purchased shipping/return label PDF onto peel-and-stick half-sheet sticker stock (Avery 8126/5126, 2 labels per sheet) via the half-sheet-label CLI. Auto-invoke whenever a shipping label, return label, or carrier QR-label PDF needs to land on a physical package: EasyPost, Pirate Ship, USPS Click-N-Ship, Amazon or vendor return labels, browser save-as-PDF labels. Use INSTEAD of printing the label on plain paper and cutting and taping it. NOT for QR codes or images on plain paper (use print-qr)."
tags: [printing, shipping, labels, returns]
freshness:
  last_refreshed_at: 2026-10-15
  auto_refresh_cadence: quarterly
---

# Half-sheet label: shipping label PDFs onto sticker stock

Canonical path for printing any **rendered label PDF** (barcode-bearing shipping label, return
label, carrier QR sheet) onto a **peel-and-stick sticker**: no cutting, no taping.

**Tool:** [`half-sheet-label`](https://github.com/johntrandall/half-sheet-label). It imposes the
label onto one half of an Avery 8126/5126 half-sheet (5.5×8.5", 2-up), trims it out of a
full page automatically, keeps it at 100% scale so the barcode stays scannable, and prints to
the tray that holds the label stock.

## Setup (once per Mac)

```bash
brew tap johntrandall/tap
brew install half-sheet-label
half-sheet-label --install-pdf-service   # optional: adds "Half-Sheet Label" to the macOS print dialog
```

Then tell it which printer and tray hold the sticker stock, in
`~/.config/half-sheet-label/config.toml`:

```toml
[printer]
name = "Office_Laser"      # your CUPS queue name: see `lpstat -p`
label_slot = "Tray2"       # the InputSlot keyword of the tray with label stock: see `lpoptions -p <name> -l | grep -i slot`
```

Without a config file the tool falls back to its author's printer name, which will not exist
on your Mac: set the config, or pass `-P <printer>` and `--slot <slot>` every time.
If the user has not set this up yet, ask which printer and tray hold the label stock, write the
config, and confirm with `half-sheet-label <pdf> --dry-run` before the first real print.

## Usage

```bash
half-sheet-label ~/Downloads/label.pdf            # impose and print the sticker
half-sheet-label label.pdf --preview              # open the imposed page in Preview, don't print
half-sheet-label label.pdf --dry-run              # show the lp command only
```

Humans can also print from any app's Print dialog: **⌘P → PDF ▾ → Half-Sheet Label** (after
`--install-pdf-service`).

## Agent procedure

1. Save the label PDF somewhere stable, for example `~/Downloads/<vendor>-<tracking>.pdf`.
2. `half-sheet-label <pdf>`. With the config set, the defaults are right: configured printer,
   label tray, top half.
3. Check that the job finished: `lpstat -W completed | head -2`.
4. Tell the user: **peel and stick**. If the sheet's second label is still free, flip the sheet
   in the tray to use it next time. The tool always prints the top half, because re-feeding a
   peeled sheet can jam the printer.

## Config and state

- Config: `~/.config/half-sheet-label/config.toml` (printer name and label tray keyword).
- State: `~/.local/state/half-sheet-label/state.json` (per-printer `next_half` tracking).
- Multi-page carrier PDFs: the label region is trimmed out of a full page automatically
  (`--no-trim` to disable). A too-tall label is rotated to fit (`--no-rotate` to disable);
  `--no-scale` errors instead of ever shrinking.

## Which label skill?

| Situation | Use |
|---|---|
| Purchased shipping/return label PDF (barcode) → sticker on a box | **this skill** |
| QR code or image with a caption on plain paper (drop-off QR, Wi-Fi QR) | `print-qr` (this plugin) |

## Failure modes

- **No label stock in the label tray**: the job prints on whatever is there. Confirm the stock
  with the user before a large batch.
- **Printer offline**: the job queues; check `lpstat -p <printer>`. Fall back to plain paper,
  cut and tape, only if the user needs the label now.
- **Wrong tray keyword**: the job comes out of the default tray. List the valid keywords with
  `lpoptions -p <printer> -l | grep -i slot` and fix `label_slot`.
