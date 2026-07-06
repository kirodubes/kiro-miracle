# kiro-miracle

The **miracle-wm desktop edition** of Kiro — the Mir-based tiler of the Kiro Wayland line (sibling to [kiro-hyprland](https://github.com/kirodubes/kiro-hyprland) and [kiro-sway](https://github.com/kirodubes/kiro-sway)).

## What it is

A configuration package: the source-of-truth config tree for Kiro's miracle-wm edition. miracle-wm
is a Wayland tiling window manager built on **Mir** (Canonical's compositor library) — the odd one
out of the line, which is otherwise wlroots/smithay. It exposes an **i3/Sway-compatible IPC**, so the
familiar ecosystem still works: this edition ships the same **waybar + mako + swaybg** shell as
kiro-sway, and waybar uses its **native `sway/workspaces` module** over miracle's IPC socket. The
SUPER grammar and Kiro app launchers match the rest of the family.

## What it ships

- `etc/skel/.config/miracle-wm/config.yaml` — miracle's single-file YAML config: `action_key`, gaps,
  border, `startup_apps` autostart, `default_action_overrides`, and the Kiro `custom_actions`
  (CTRL+ALT + SUPER+F1..F12 launchers).
- `etc/skel/.config/waybar/config-miracle.jsonc` — the bar layout (native sway modules). The waybar
  `style.css`/`colors.css` and the mako notification config come from **`kiro-wayland-dotfiles`** (a
  hard dependency shared across the editions), so this package ships only the miracle-specific config.
- `etc/skel/.config/miracle-wm/{keybindings.txt,bg/kiro.jpg}` — the cheat sheet and wallpaper.

## Keybindings

miracle-wm's own defaults are already SUPER-based, so Kiro keeps its native tiling binds (SUPER+arrows
to focus, SUPER+Shift+arrows to move, SUPER+v/h to split, SUPER+r resize-mode, SUPER+1..0 workspaces)
and overlays the Kiro app launchers. Press **Super + Ctrl + S** for the searchable cheat sheet.

## How to install

```sh
sudo pacman -S kiro-miracle
```

`kiro-miracle` depends on `miracle-wm` plus the waybar shell stack (all resolved from the Kiro
`nemesis_repo` / chaotic-aur / official). On a fresh login miracle-wm starts the bar and wallpaper.

A pristine copy of the config is kept at `/usr/share/kiro/kiro-miracle/` so it can be restored.

> **Note:** miracle-wm is young and Mir-based. Screen lock/idle (swaylock/swayidle) and screencast
> portals are **optional** and unverified on Mir — see the package `optdepends` and `CLAUDE.md`.
