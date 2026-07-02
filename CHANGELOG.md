# Changelog

All notable changes to **kiro-sway** are documented here.
Format: one dated entry per day (`YYYY.MM.DD`), newest first.

## 2026.07.02

### What Changed
- **Moved `/etc/dconf/profile/user` + `/etc/dconf/db/local.d/00-kiro.conf` to
  `kiro-wayland-dotfiles`.** All 9 Kiro Wayland editions had shipped identical copies of these
  files, which conflicted in pacman whenever two editions were installed on the same machine.
  Settings unchanged — now owned once in the shared package (already a dependency here).

### Files Modified
- `etc/dconf/` (removed, moved to `kiro-wayland-dotfiles`)

## 2026.07.01

### What Changed
- **Added Variety wallpaper-rotator autostart + keybinds.** `variety` (configured by
  `kiro-variety-config`) now autostarts alongside the existing static `swaybg` wallpaper. Ported
  the ohmychadwm `keybindings.txt` scheme (Mod1+N/P/T/F/arrows/Up/Down/W): next/previous/trash/
  favorite/pause/resume/selector, plus Mod1+Shift+N/P/T/F/U combos that also trigger this
  edition's `set-theme.sh` pywal recolor (waybar/mako/`config.d/35-colors`). `variety` +
  `kiro-variety-config` added to `depends=()`.

### Technical Details
- sway's `exec` treats a bare `;` as its own command separator, so the recolor combos are wrapped
  in `exec sh -c '...; ...'` (matching how other multi-step sway commands in this file avoid the
  ambiguity), rather than chaining bare `exec cmd1; exec cmd2` as an early draft did.
- Verified no existing `Mod1+`(alt) binds collided with the new letters/arrows before adding (only
  `Ctrl+Mod1` combos existed on those keys).

### Files Modified
- [etc/skel/.config/sway/config.d/60-keybindings](etc/skel/.config/sway/config.d/60-keybindings)
- [etc/skel/.config/sway/config.d/40-autostart](etc/skel/.config/sway/config.d/40-autostart)
- [etc/skel/.config/sway/keybindings.txt](etc/skel/.config/sway/keybindings.txt)
- [../KIROTUX-PKG-BUILD/kiro-sway/PKGBUILD](../KIROTUX-PKG-BUILD/kiro-sway/PKGBUILD)

## 2026.06.30

### What Changed
- **Moved shared dotfiles into the new `kiro-wayland-dotfiles` base** — mako, hyprlock/hypridle,
  and the waybar `colors.css`/`style.css` now come from that package (resolves the cross-edition
  file conflict, e.g. kiro-hyprland ↔ kiro-river both owning `~/.config/mako/config`). This edition
  now ships only its `waybar/config-<wm>.jsonc` and launches `waybar -c` against it.
- **Initial config package** for the Kiro sway edition — the rock-solid i3-style tiler of the
  KIROTUX Wayland line. Ships the same waybar + mako + swaybg shell as kiro-hyprland, so the look
  and feel match the Hyprland edition; the SUPER keybind grammar is ported onto sway's i3 vocabulary.
- **Fixed waybar appearing ~25s late at login** — `config.d/40-autostart` now launches
  `env GTK_A11Y=none waybar`, skipping the at-spi accessibility bus that stalled the GTK bar before
  it painted (same fix applied across the Wayland line; first found on kiro-river).
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
