# kiro-sway

The **sway desktop edition** of Kiro — the rock-solid, i3-style tiler of the Kiro Wayland line (sibling to [kiro-hyprland](https://github.com/kirodubes/kiro-hyprland)).

## What it is

A configuration package: the source-of-truth config tree for Kiro's sway edition. sway is the
original, most-deployed wlroots tiling compositor, with an i3-compatible config. It ships the same
**waybar + mako + swaybg** shell as kiro-hyprland — so the look and feel match — and because waybar
was written for sway first, it uses the **native `sway/workspaces` module** (real workspace pills,
no workaround). The edition ships **swayfx** (the eye-candy sway fork: blur, rounded corners,
shadows) so it stays visually in step with hyprland; plain sway is a one-line swap.

## What it ships

- `etc/skel/.config/sway/config` + `config.d/*` — modular config: `10-input`, `20-output`,
  `30-appearance` (swayfx effects), `35-colors` (pywal-generated), `40-autostart`,
  `50-window-rules`, `60-keybindings`. SUPER grammar + CTRL+ALT + SUPER+F1..F12, autotiling.
- `etc/skel/.config/waybar/` — the bar (`config.jsonc`, `style.css`, pywal-driven `colors.css`).
- `etc/skel/.config/mako/config` — notifications.
- `etc/skel/.config/sway/{keybindings.txt,bg/kiro.jpg,scripts/}` — cheat sheet, wallpaper, and the
  pywal `set-theme.sh` + `import-gsettings.sh`.
- `etc/skel/.config/hypr/{hyprlock.conf,hypridle.conf}` — the Wayland lock pipeline.

## Theming — pywal

One wallpaper drives every colour. At login `set-theme.sh` runs pywal on the wallpaper and fans
the palette out to waybar, mako, and the sway window borders, then live-reloads each. Re-theme any
time with `~/.config/sway/scripts/set-theme.sh /path/to/wallpaper.jpg`.

## How to install

```sh
sudo pacman -S kiro-sway
```

`kiro-sway` depends on `swayfx` + the waybar stack + `python-pywal` (swayfx from chaotic-aur/cachyos,
the rest from `extra`). On a fresh login sway starts the bar and wallpaper. Press
**Super + Ctrl + S** for the searchable keybindings cheat sheet.

A pristine copy of the config is kept at `/usr/share/kiro/kiro-sway/` so it can be restored.
