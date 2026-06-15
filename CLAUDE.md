# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Is

ZMK firmware configuration for a 5-column Corne split keyboard using nice_nano_v2 boards. There is no local build — firmware is compiled by GitHub Actions on every push/PR using ZMK's `build-user-config.yml` workflow (ZMK v0.3).

## Build & Deploy

Push to GitHub to trigger a firmware build. The workflow produces `.uf2` files for left half, right half, and settings_reset (defined in `build.yaml`). There are no local build commands, tests, or linters.

## Architecture

All configuration lives in `config/`:

- **`corne.keymap`** — the main file you'll edit. Devicetree syntax defining 7 layers, combos, macros, and behaviors. Key positions 0–35 are defined as `POS_*` macros at the top.
- **`corne.conf`** — Kconfig toggles (display, mouse/pointing, BLE power, debounce, sleep timeouts).
- **`west.yml`** — West manifest pinning ZMK to v0.3.
- **`corne.json`** — physical key layout metadata for ZMK Studio.

## Keymap Structure

7 layers indexed 0–6 via `LYR_*` defines: BASE, NUM, NAV, MOU (mouse simulation), APP, SYM, BLU (bluetooth).

**Home row mods** use bilateral `hold-tap` behaviors (`hml`/`hmr`) with `hold-trigger-key-positions` restricting activation to the opposite hand. Tuning values: `tapping-term-ms=280`, `require-prior-idle-ms=150`, `quick-tap-ms=175`.

**Combos** are layer-scoped (check the `layers` property). Some key combos like `SD` and `DF` have different bindings on BASE vs APP layers.

**APP layer** left-hand keys send `Cmd+Shift+Ctrl+<key>` — these are Raycast hotkeys that launch specific apps. Right-hand has macOS screenshot shortcuts and window management.

**`ltl` (layer-tap-long)** is a custom hold-tap with `tapping-term-ms=350` used for Space (SYM layer) and Enter (APP layer) thumb keys.

## Editing Guidelines

- Combo timeouts use three tiers: `COMBO_TIMEOUT_DEFAULT` (50ms), `COMBO_TIMEOUT_MEDIUM` (75ms), `COMBO_TIMEOUT_LONG` (125ms).
- When adding combos, specify a `layers` property to avoid unintended triggers on other layers.
- Key positions use named defines (`POS_Q`, `POS_W`, etc.) — use these instead of raw numbers.
- The `/**/` column separator in binding rows is a formatting convention for visual alignment of left/right halves.
