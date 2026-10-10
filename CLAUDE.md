# kiro-sway — Claude project instructions

## Overview
Config package for the **Kiro sway edition** — the rock-solid i3-style tiler of the KIROTUX Wayland
line (sibling to [kiro-hyprland](../kiro-hyprland/CLAUDE.md)). Public, open-core, shipped via
`nemesis_repo`. Full research + decisions live in the internal `Kiro-HQ/Kirotux/study-of-sway.md`.

## Edition spec (the WM-variable matrix)
- **Compositor:** **swayfx** (the eye-candy sway fork — blur/rounded corners/shadows). Config-
  compatible with upstream sway; swayfx `Provides`/`Conflicts` sway, so it replaces it. To ship
  plain sway, swap `swayfx` → `sway` in the PKGBUILD `depends` — the config tree is unchanged
  (the swayfx effect lines in `30-appearance` are simply ignored by upstream sway).
- **Config language:** i3-style, **modular** `~/.config/sway/config` (entrypoint: variables +
  `include config.d/*`) → `config.d/NN-*` snippets (sorted-glob load order). Edit the snippet, not
  the entrypoint.
- **Desktop shell:** **waybar + mako + swaybg** — same stack as kiro-hyprland. waybar uses its
  **native** `sway/workspaces` modules (the gap wayfire papered over with `wlr/taskbar`).
- **Autostart:** sway's `exec`/`exec_always` in `config.d/40-autostart` (run via `/bin/sh -c`).
  `autotiling` for automatic split direction. XDG-autostart-only items (`xdg-user-dirs-update`)
  added by hand.
- **Theming:** **pywal**. `set-theme.sh` runs pywal at login → regenerates `waybar/colors.css`,
  rewrites mako + the sway `config.d/35-colors` block, then `swaymsg reload`. Same pipeline shape
  as kiro-wayfire (sway colours are hex, so no float conversion).
- **Lock/idle:** hyprlock + hypridle (wlroots-compatible), reusing kiro-hyprland's `hyprlock.conf`
  + the betterlockscreen blur cache — matches kiro-wayfire for cross-edition lock consistency.
  (swaylock-effects is the sway-native alternative, kept as an optdepend.)
- **Dependencies:** `swayfx` from chaotic-aur/cachyos; everything else from `extra` — **nothing
  into `nemesis_repo`**. This is the cleanest packaging gate in the line alongside wayfire.

## Keybindings
- SUPER grammar ported from kiro-hyprland in i3 vocabulary (`bindsym`): CTRL+ALT launchers +
  SUPER+F1..F12 + `kiro-keybindings` on SUPER+CTRL+S. Arrows = focus/move, h/j/k/l = resize,
  `workspace number 1..10`, scratchpad. Manual tiling (not master/dwindle/column).
- `etc/skel/.config/sway/keybindings.txt` mirrors `config.d/60-keybindings` — keep in lockstep; a
  duplicate-chord scan must pass. (`kiro-keybindings` / `/kiro-create-keybindings` still need
  **sway** in their WM-detection table — known gap, same one Hyprland/niri/wayfire hit.)

## Patterns / gotchas
- **swayfx vs sway is a one-line `depends` swap** — the config is identical; swayfx effect lines in
  `30-appearance` are no-ops on upstream sway. Decision is reversible at the package level.
- **`app_id` not `class`** in `for_window` rules — `class` only matches XWayland clients.
- **Wallpaper is set once** by swaybg in `40-autostart`; the native `output * bg` line is left out
  to avoid a double-set race.
- Config **not yet validated on a real sway/swayfx boot** — run `sway --validate` on a real session
  before the first ISO test (same caveat niri/wayfire carried). Confirm the swayfx `layer_effects`
  syntax and that swayfx tracks the live sway version (study §6 flagged the version-lag risk).

## Build / delivery
- Source-of-truth for the config; delivered as the `kiro-sway` package via
  `~/KIRO-PKG-BUILD-APPS/kiro-sway/build.sh` (public recipe → `~/EDU/nemesis_repo/`). After editing
  here: rebuild the package (recipe `build.sh`), then the ISO to test a fresh install.
- See [KIROTUX/CLAUDE.md](../../KIROTUX/CLAUDE.md) for the full KIROTUX delivery architecture.
