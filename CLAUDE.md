# kiro-miracle — Claude project instructions

## Overview
Config package for the **Kiro miracle-wm edition** — the **Mir-based** tiler of the KIROTUX Wayland
line (sibling to [kiro-sway](../kiro-sway/CLAUDE.md), which it is modelled on). Public, open-core,
shipped via `nemesis_repo`. Full research + decisions live in the internal
`Kiro-HQ/Kirotux/study-of-miracle.md`.

miracle-wm is the **odd one out**: every other edition is wlroots/smithay; this one is built on **Mir**.
It works because miracle exposes an **i3/Sway-compatible IPC** — so waybar, swaymsg and the rest of the
sway shell transfer over.

## Edition spec (the WM-variable matrix)
- **Compositor:** **miracle-wm** (Wayland tiler on Mir). **AUR-only** — not in official/chaotic-aur/
  cachyos — so `miracle-wm-git` is vendored into `nemesis_repo` (see Build/delivery). Package name is
  `kiro-miracle` (family convention: compositor short name, no `-wm` suffix).
- **Config language:** miracle's **single-file YAML**, `~/.config/miracle-wm/config.yaml` (NOT modular
  like sway's `config.d`). Schema read from upstream source. Sections: `action_key`, `terminal`,
  `environment_variables`, `inner_gaps`/`outer_gaps`, `border` (RGBA **0-255 int arrays**),
  `startup_apps`, `default_action_overrides`, `custom_actions`. Hot-reload: SUPER+Shift+R.
- **Desktop shell:** **waybar + mako + swaybg** — same stack as kiro-sway, and shared the same way.
  waybar uses its native `sway/workspaces` module over miracle's IPC socket. Like every other waybar
  edition, kiro-miracle ships **only** `waybar/config-miracle.jsonc`; the shared `mako/config`, waybar
  `style.css` and `colors.css` come from **`kiro-wayland-dotfiles`** (a hard `depends`). This is what
  keeps the editions co-installable — mako reads a fixed path (`~/.config/mako/config`) that cannot be
  namespaced, so two editions each shipping it would be a pacman file conflict. waybar is launched with
  an explicit `-c ~/.config/waybar/config-miracle.jsonc` (so the right config loads) and picks up the
  shared `style.css` sitting next to it; mako launches bare (reads the shared config at its default path).
- **Autostart:** miracle's **`startup_apps`** list (command + restart_on_death) — NOT sway `exec`, NOT
  XDG. `xdg-user-dirs-update` is added explicitly as a startup_app. The X11
  `/kiro-twm-autostart-check` does not apply to this Wayland edition.
- **Keybindings:** `action_key: meta` = SUPER. miracle's own defaults are already SUPER-based, so we
  KEEP the native tiling binds (SUPER+arrows select, +Shift move, SUPER+v/h split, SUPER+r resize-mode,
  SUPER+1..0 workspaces, SUPER+Q close, SUPER+f fullscreen). `default_action_overrides` move the
  SUPER+E quit_compositor **footgun** → SUPER+Shift+E, and set reload/floating/tabbing/stacking to Kiro
  chords. `custom_actions` overlay the Kiro launchers (CTRL+ALT + SUPER+F1..F12, `kiro-keybindings` on
  SUPER+Ctrl+S). Layout vocabulary is **miracle-native** (vertical/horizontal request + resize-mode),
  not i3-split or master/dwindle.
- **Theming:** the **`border`** in `config.yaml` keeps the static Kiro accent (`focus_color` =
  rgb(77,143,214)) — that's miracle-native and namespaced, so it stays. The **bar/notifications** are
  themed by `kiro-wayland-dotfiles`' shared `style.css` + `colors.css` (static Tokyo Night for the
  non-pywal editions), NOT a miracle-owned stylesheet. **pywal deferred** — miracle colours are YAML
  RGBA int-arrays, so a pywal `set-theme` would have to rewrite the YAML block (unlike sway's hex); not
  worth it for the first cut. Net look: blue Kiro window border with the shared Tokyo-Night waybar/mako.
- **Lock/idle:** **optdepend only** (swaylock + swayidle). Mir's `ext-session-lock-v1` support is
  **unverified** — kept out of the hard shell to avoid shipping a broken lock. Top validation risk.

## Keybindings
- `etc/skel/.config/miracle-wm/keybindings.txt` mirrors `config.yaml` — keep in lockstep; a
  duplicate-chord scan must pass. Written in miracle's own vocabulary.
- `kiro-keybindings` / `/kiro-create-keybindings` still need **miracle** in their WM-detection table —
  known gap, same one Hyprland/niri/sway/wayfire hit.

## Patterns / gotchas
- **Command spawning** — `startup_apps`/`custom_actions` assume miral launches via a shell (`sh -c`),
  so `~`, pipes (`grim | wl-copy`) and `$(slurp)` work. Very likely but **unverified on this box** —
  and the autostart already relies on it (`swaybg -i ~/…`, `waybar -c ~/…`). mako still launches bare
  (default path), polkit at an absolute path.
- **No `xdg-desktop-portal-wlr`** — it's wlroots-only and won't function on Mir. Ships
  `xdg-desktop-portal-gtk` (file-chooser); screencast is a known gap on Mir.
- **App-launcher binds reference apps that may not be installed** (brave, opera, vivaldi, …) — same as
  kiro-sway; they're on-demand launchers, not hard deps.
- Config **not yet validated on a real miracle-wm boot** (miracle-wm not installed on this box). Verify
  on a QEMU virtio-gpu VM or real metal — **not VirtualBox** (see memory `niri-virtualbox-needs-3d-accel`;
  Mir needs a real render node). Check: waybar binds to the IPC, swaybg draws, and the lock/idle risk.

## Build / delivery
- Source-of-truth for the config; delivered as `kiro-miracle` via
  `../KIROTUX-PKG-BUILD/kiro-miracle/build.sh` (public recipe → `~/EDU/nemesis_repo/`).
- **miracle-wm itself** is built from AUR into `~/KIRO-PKG-BUILD-3PARTY/miracle-wm-git/` → `nemesis_repo`.
  Build gotcha (patched in that recipe): `wasmedge-bin` ships headers flat in `/usr/include/` with no
  `wasmedge/` subdir, but miracle does `#include <wasmedge/wasmedge.h>` — `build()` symlinks a private
  `wasmedge/` dir and passes `-DWASMEDGE_INCLUDE_DIR`. Build-time only; runtime just needs `libwasmedge.so`.
- See [../CLAUDE.md](../CLAUDE.md) for the full KIROTUX delivery architecture.
