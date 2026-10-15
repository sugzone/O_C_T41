# 4U Loudest Warning port: working notes

**Git: the user makes all commits in this repo. Don't commit, push, or suggest commits.**

## Goal
Adapt O_C_T41 (Eurorack) to the 4U Loudest Warning format with a **3" NPW** front panel.
- Refs: https://github.com/PaulStoffregen/O_C_T41 · http://www.loudestwarning.co.uk/portfolio/4u-modular-specs/ · https://github.com/mixelpixx/Konnect
- Work in KiCad 10 through the Konnect MCP server.

## Planned changes
1. **New panel**: combine the elements of `14HP_PANEL` + `2HP_EXP_USB_A` + `2HP_EXP_USB_C` into one 3" NPW panel. The user is choosing between a couple of layouts and hasn't shared them yet.
2. **Modified analog_rev3**: add the circuits from the 2HP_EXP_USB_(A/C) boards and widen the board to match the panel.
3. **Modified control_rev3**: widen it so its pin headers line up with the new analog board.
- Proposed (not yet approved): put new boards in new project copies and leave the rev3 originals untouched.

## Current boards (Edge.Cuts outlines, KiCad file version 20241229)
| Board | Path | Outline | Footprints |
|---|---|---|---|
| analog_rev3 | hardware/analog_rev3/ | 50.80 × 107.95 mm | 202 |
| control_rev3 | hardware/control_rev3/ | 48.26 × 105.41 mm | 127 |
| 14HP_PANEL | hardware/14HP_panel_and_expanders/14HP_PANEL/ | 71.12 × 128.50 mm | 43 |
| 2HP_EXP_USB_A | hardware/14HP_panel_and_expanders/2HP_EXP_USB_A/ | 10.16 × 99.22 mm | 11 |
| 2HP_EXP_USB_C | hardware/14HP_panel_and_expanders/2HP_EXP_USB_C/ (files are named `2HP_EXT_USB_C.*`) | 10.16 × 99.22 mm | 15 |

## Status (2026-10-15)
- Added `.mcp.json` at the repo root to register Konnect (stdio; binary at `~/Documents/KiCad/10.0/3rdparty/plugins/com_github_mixelpixx_konnect/bin/konnect`). It contains an absolute local path. Still undecided whether to commit it or gitignore it.
- Next: confirm Konnect appears in `/mcp`. KiCad needs Preferences → Plugins → "Enable KiCad API" turned on, and a board open.
- Then step 1 of the plan: fetch the Loudest Warning spec numbers (panel height, 3" NPW width, mounting holes, rail clearance, max depth). **None of these are verified yet. Don't quote any from memory.**

## Open questions for the user
- Which panel layout? (Ask them to share both.)
- New project copies vs. editing rev3 in place?
- Commit `.mcp.json` or gitignore it?
