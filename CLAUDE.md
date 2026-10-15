# 4U Loudest Warning port: working notes

**Git: the user makes all commits in this repo. Don't commit, push, or suggest commits.**

## Goal
Adapt O_C_T41 (Eurorack) to the 4U Loudest Warning format with a **3" NPW** front panel.
- Refs: https://github.com/PaulStoffregen/O_C_T41 · http://www.loudestwarning.co.uk/portfolio/4u-modular-specs/ · https://github.com/mixelpixx/Konnect
- Work in KiCad 10 through the Konnect MCP server.

## Planned changes
1. **New panel**: combine the elements of `14HP_PANEL` + `2HP_EXP_USB_A` + `2HP_EXP_USB_C` into one 3" NPW panel. The user is choosing between a couple of layouts and hasn't shared them yet.
2. **Modified analog_rev3**: widen the board to match the panel. (The original plan was to fold in the 2HP_EXP_USB_(A/C) circuits; superseded, see Design decisions.)
3. **Modified control_rev3**: widen it so its pin headers line up with the new analog board.
- Decided: new boards go in new project copies; the rev3 originals stay untouched.

## Design decisions (2026-10-15, planning stage)
- Panel drafts (two layouts): `~/Documents/affinity photo/orn8/orn8 screen high.pdf` and `orn8 screen low.pdf`. Panel name "ORN8", footer "CYBERCRIMINAL". The two layouts have the same elements; only the vertical order differs. **Still undecided which one.**
- **Jacks**: banana jacks for all signal I/O (audio included) instead of 3.5 mm TS. MIDI in/out uses 3.5 mm TRS stereo jacks, in on the left and out on the right.
- **Arrangement**: inputs on the left, outputs on the right, a middle column of trigger inputs (labels sit to the left of the jacks, marking them as inputs).
  - As drawn: left has inputs 1–8 and in L/R; middle has trigger inputs 1–4; right has outputs A–H and out L/R.
- **Controls**: two encoders (large green circles), buttons A/X/B/Y, plus a "Z" button (the one with the smiley label).
- **Screen**: a larger screen compatible with the Phazerville firmware. Model TBD.
- **USB**: USB-C only; it replaces the original "host" breakout. The USB-A expander is dropped. Undecided between:
  1. USB-C breakout board off our PCB with its own external power; our PCB only routes traces to the relevant Teensy 4.1 pins.
  2. A cable straight from the Teensy's USB port, with no circuitry on our boards.
- **Graphics only (not parts)**: all text and jack labels; the MIDI icon; the decorative outline wrapping the USB port and both MIDI TRS jacks (it marks them as MIDI-capable); and two horizontal lines, each 11.7 mm in from the top and bottom panel edges.
- **Parts**: link as many as possible through the JLCPCB plugin in KiCad, then make a separate BOM for whatever JLCPCB doesn't stock. Part numbers come later.

## Trigger pull-down (2026-10-15)
**Decision:** a 100k resistor from each of the 4 trigger jack tips to GND, ahead of the existing 100k input resistor. **Provisional; it must be tested.**

**Limits (hand calculation, not yet simulated or tested):**
- Upper limit, to stop latching: unpatched with the output high at 3.3 V, the + input sits at 3.3 × (100k + Rp) / (1M + 100k + Rp). This must be below 1.5 V, so Rp < ≈730k. With Rp = 100k it's ≈0.5 V.
- Lower limit, source loading: the load is ≈ Rp ∥ 1.1M ≈ 92k. A 5 V trigger stays above 1.65 V if the source output resistance is < ≈180k. Real source output resistances aren't verified.
- Overvoltage: 12 V into 100k draws 0.12 mA, which is harmless.
- When patched, the thresholds (≈1.65 V rising, ≈1.32 V falling) don't change.

**Test options (none chosen yet):**
- ngspice in KiCad. This needs an LM339 SPICE model; TI's availability hasn't been checked.
- Breadboard one channel: LM339, 100k in, 1M feedback, 4.7k pull-up to 3.3 V, 1.5 V reference.
- Solder a resistor tip-to-sleeve on a trigger jack of a built O_C_T41.
- Test case: pull the cable mid-gate; the output must drop.

**4U trigger levels:**
- Serge (primary source, the official Paperface 50 user guide at serge-modular.com/docs): CV is "0 to (typically) +5V". Triggers "fall in the same voltage range ... though there are modules which produce outputs of more than +5 Volts". Inputs or outputs outside ±12 V may damage modules. The original Serge catalog (vasulka.org archive) states no trigger level.
- CGS/Elby: no primary document found. Secondary sources (sdiy.info, ModularGrid) give ~5 V for the CGS114, the CGS62, and the CGS56 (±5 V).
- Design target: 0–5 V triggers, tolerant of up to ±12 V.

**How other designs define unpatched inputs (reference only):**
- **LWOC** (github.com/dksynth/LWOC, 4U O_C, v3.0, Teensy 4.0): banana jacks with no switch; separate trigger jacks. Trigger path: 100k → MMBT3904 base (33k base-to-GND, 1N4148 clamp) → collector (33k pull-up, 470 pF) → 74HC14 → MMBT3904 → Teensy TR pin. Estimated threshold ≈2.5 V (not verified). CV inputs: banana → 100k → op-amp, with no pull-down.
- **CLee 4U Orgone Accumulator** (clsound.com docs, Teensy-based): gate input has 20k series to the Teensy plus clamp diodes and a resistor to GND. The build notes change that resistor from 100k to 51k: "With a 100K resistor here the RESET LED can sometimes latch on when nothing is plugged into the RESET jack, or if the jack is pulled while a high signal is present." It's a different circuit from ours and wasn't analysed.
- **DrJ3RK PulseLatch 1.3:** inputs to a 40106 Schmitt inverter with 10k / 100k to GND. **DrJ3RK Divider 1.1:** 10k → 2N3904 base, with 100k base-to-GND.
- **CLSound VC Timer:** the web page doesn't describe its inputs; the build-docs zip wasn't opened.
- **North Coast Synthesis "All about normalling":** switched banana sockets are "not in common use" and probably "expensive speciality parts". Its alternatives are high-resistance (≈1M) normalling to a default, a pull-down to ground, or MCU cable sensing via a GPIO through a resistor. Cable sensing would need Phazerville firmware changes and is awkward behind the LM339.

## Current boards (Edge.Cuts outlines, KiCad file version 20241229)
| Board | Path | Outline | Footprints |
|---|---|---|---|
| analog_rev3 | hardware/analog_rev3/ | 50.80 × 107.95 mm | 202 |
| control_rev3 | hardware/control_rev3/ | 48.26 × 105.41 mm | 127 |
| 14HP_PANEL | hardware/14HP_panel_and_expanders/14HP_PANEL/ | 71.12 × 128.50 mm | 43 |
| 2HP_EXP_USB_A | hardware/14HP_panel_and_expanders/2HP_EXP_USB_A/ | 10.16 × 99.22 mm | 11 |
| 2HP_EXP_USB_C | hardware/14HP_panel_and_expanders/2HP_EXP_USB_C/ (files are named `2HP_EXT_USB_C.*`) | 10.16 × 99.22 mm | 15 |

## Status (2026-10-15)
- Added `.mcp.json` at the repo root to register Konnect (stdio; binary at `~/Documents/KiCad/10.0/3rdparty/plugins/com_github_mixelpixx_konnect/bin/konnect`). It contains an absolute local path, so it's gitignored (`.gitignore` added; untracked with `git rm --cached`).
- Next: confirm Konnect appears in `/mcp`. KiCad needs Preferences → Plugins → "Enable KiCad API" turned on, and a board open.
- Then step 1 of the plan: fetch the Loudest Warning spec numbers (panel height, 3" NPW width, mounting holes, rail clearance, max depth). **None of these are verified yet. Don't quote any from memory.**

## Open questions for the user
- Which panel layout, high or low?
- USB-C: off-board breakout (option 1) or direct cable (option 2)?
- Part numbers: screen, encoders, banana jacks, TRS jacks.
- Trigger pull-down: 100k is **provisionally approved** but **must be tested** (bench test or simulation; method not chosen yet). See the Trigger pull-down section.

## Jack switching findings (analog_rev3, traced from the .kicad_pcb)
PJ398SM jacks have a switch pin (TN) that's normalled to the tip; plain banana jacks usually don't have one.
- **Outputs** (J9–J16 DAC A–H, J18/J20 audio out): TN not connected, so losing it has no effect.
- **CV inputs** (J1–J8): TN goes to GND. The tip runs through 100k into an MCP6002 inverting input (virtual ground), so floating and grounded give the same result. The only cost of floating is slightly more noise pickup.
- **Audio in** (J17/J19): TN goes to GND. The tip runs through 1 µF and 100k into an MCP6022 inverting input. Same reasoning as the CV inputs: no functional change.
- **Trigger inputs**: in the original, the J1–J4 tips also feed triggers 1–4. The path goes through bridged solder jumpers JP1–JP4 to headers J24/J25 ("Trigger Inputs"), then through 100k to the LM339 + inputs. Each comparator has 1M positive feedback (hysteresis), and its threshold is `/THRES`, which comes from the control board via J26.5. If the tip is grounded, an unpatched trigger reads "off". If the tip floats, the comparator's input sees only its own output through the 1M, so it latches in its last state (e.g. a trigger stuck high after a cable is pulled mid-gate). This assumes `/THRES` is between 0 V and 3.3 V, which hasn't been verified.
- **`/THRES` checked: ≈1.50 V.** It's a divider on control_rev3 (R125 11k from +5VA, R124 4.7k to GND), reaching analog J26.5 via control J30.5. J26↔J30 was matched by identical power-pin pattern; physical header positions weren't checked. Trigger thresholds come out to ≈1.65 V rising and ≈1.32 V falling. Floating-input behaviour: if the output is low it stays off (fine); if the output is high, the + input sits near 3.3 V and **latches on**. Concern confirmed.
- The LM339 with 1M positive feedback is already a Schmitt trigger. Hysteresis gives a clean on/off for patched signals, but it can't define the level of an unpatched input; that needs a pull-down or some other means. Swapping in a Schmitt-trigger IC doesn't fix it.
- **Decided**: keep the original LM339 trigger architecture (U1 on analog_rev3, with R27–R30 100k in, R31–R34 1M feedback, and the `/THRES` divider); add a pull-down instead of redesigning. The user found no clear benefit in the LWOC approach.
- **Decided**: separate trigger jacks, so 24 jacks instead of 20. This splits CV 1–4 from triggers 1–4; leaving JP1–JP4 open separates the two paths. Splitting doesn't fix the floating-trigger issue, because the new trigger jacks are banana jacks too.
