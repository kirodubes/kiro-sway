# Changelog

All notable changes to **kiro-sway** are documented here.
Format: one dated entry per day (`YYYY.MM.DD`), newest first.

## 2026.06.30

### What Changed
- **Initial config package** for the Kiro sway edition — the rock-solid i3-style tiler of the
  KIROTUX Wayland line. Ships the same waybar + mako + swaybg shell as kiro-hyprland, so the look
  and feel match the Hyprland edition; the SUPER keybind grammar is ported onto sway's i3 vocabulary.
- **swayfx as the shipped compositor** — the eye-candy sway fork (blur, rounded corners, shadows,
  dim-inactive) so the edition stays visually in step with hyprland/wayfire. Config-compatible with
  upstream sway; plain `sway` is a one-line `depends` swap.
- **Native waybar workspaces** — sway's `sway/workspaces`/`sway/mode`/`sway/window` modules work
  natively (the gap wayfire had to paper over with `wlr/taskbar`).
- **pywal theming** — one wallpaper drives every colour: `set-theme.sh` runs pywal at login and
  fans the palette out to waybar, mako, and sway's window borders, then live-reloads each.

### Technical Details
- `etc/skel/.config/sway/config` is a thin entrypoint (variables + `include config.d/*`); the real
  config is the modular `config.d/*` split modelled on the EndeavourOS Sway layout: `10-input`,
  `20-output`, `30-appearance` (swayfx effects — ignored by plain sway), `35-colors`
  (pywal-regenerated), `40-autostart`, `50-window-rules`, `60-keybindings`.
- Keybinds in i3 grammar (`bindsym`): app launchers (CTRL+ALT + SUPER+F1..F12), arrows for
  focus/move, h/j/k/l for resize, `workspace number 1..10`, scratchpad, `autotiling` for automatic
  split direction. `kiro-keybindings` on SUPER+CTRL+S.
- **Shell ported from kiro-hyprland** — `waybar/config.jsonc` keeps the same layout with native
  sway modules; `mako/config` + `style.css` carry Tokyo Night defaults, overwritten at login by pywal.
- **Lock pipeline** = hyprlock + hypridle, reusing kiro-hyprland's `hyprlock.conf` + the
  betterlockscreen blur cache (matches kiro-wayfire for cross-edition consistency).
- `set-theme.sh` (pywal) — regenerates `waybar/colors.css`, rewrites the mako colour lines and the
  sway `config.d/35-colors` block, then SIGUSR2-reloads waybar + `makoctl reload` + `swaymsg reload`.
- Everything from `extra` + chaotic-aur (`swayfx`) — **nothing built into `nemesis_repo`**.

### Files
- `etc/skel/.config/sway/config` + `config.d/{10-input,20-output,30-appearance,35-colors,40-autostart,50-window-rules,60-keybindings}`
- `etc/skel/.config/sway/{keybindings.txt,bg/kiro.jpg,scripts/set-theme.sh,scripts/import-gsettings.sh}`
- `etc/skel/.config/waybar/{config.jsonc,style.css,colors.css}`
- `etc/skel/.config/mako/config`
- `etc/skel/.config/hypr/{hyprlock.conf,hypridle.conf}`
- `README.md`, `CLAUDE.md`, `up.sh`, `setup.sh`, `.gitignore`, `kiro.jpg`

### Not yet verified
- The sway config is **not validated on a real sway/swayfx boot** (not installed on the build box) —
  run `sway --validate` on a real session before/at first ISO test, same caveat niri/wayfire carried.
  In particular confirm the swayfx `layer_effects` syntax and that swayfx tracks the live sway version.
- `kiro-keybindings` / `/kiro-create-keybindings` still need **sway** added to their WM-detection
  table (known gap, same one Hyprland/niri/wayfire hit).
