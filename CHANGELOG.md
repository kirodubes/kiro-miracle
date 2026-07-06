# Changelog — kiro-miracle

All notable changes to the Kiro miracle-wm edition config package.

## 2026.07.06

### What Changed
- **Fixed co-installability** — kiro-miracle no longer ships its own `mako/config` or waybar
  `style.css`. Those paths are owned by `kiro-wayland-dotfiles` (already a hard `depends`), so the
  standalone copies were a guaranteed pacman file conflict at install time and broke co-installation
  with every other waybar edition. Aligned with the established kiro-sway model: ship **only** the
  edition's waybar config, inherit the shared shell.
- Waybar theme is now the **shared static Tokyo Night** (`kiro-wayland-dotfiles` `style.css` +
  `colors.css`) instead of the miracle-only `#4d8fd6` stylesheet. The window **border** keeps the
  static Kiro accent rgb(77,143,214) (it's miracle-native YAML, namespaced, no conflict).

### Technical Details
- Renamed `waybar/config.jsonc` → `waybar/config-miracle.jsonc` (family convention `config-<wm>.jsonc`).
- Removed `waybar/style.css` and the whole `mako/` dir (both owned by `kiro-wayland-dotfiles`).
- `config.yaml` startup_apps: waybar now launches
  `env GTK_A11Y=none waybar -c ~/.config/waybar/config-miracle.jsonc` — the `-c` loads the right
  config and waybar picks up the shared `style.css` sitting beside it; `GTK_A11Y=none` avoids the
  ~25s at-spi login stall (same as kiro-sway). mako still launches bare (reads the shared config at
  its fixed default path — mako can't be namespaced).
- Squared the CLAUDE.md/README drift that still described the standalone "bare waybar/mako, default
  paths, static waybar/mako CSS" model.

### Files Modified
- `etc/skel/.config/miracle-wm/config.yaml`
- `etc/skel/.config/waybar/config-miracle.jsonc` (renamed from `config.jsonc`)
- removed `etc/skel/.config/waybar/style.css`, `etc/skel/.config/mako/config`
- `CLAUDE.md`, `README.md`, `CHANGELOG.md`

## 2026.07.05

### What Changed
- **Initial config package** for the Kiro miracle-wm edition (`kiro-miracle`) — the Mir-based tiler of
  the KIROTUX Wayland line, modelled on kiro-sway (miracle-wm speaks the sway/i3 IPC).

### Technical Details
- `config.yaml` authored in miracle's native single-file YAML schema (read from upstream source):
  `action_key: meta` (SUPER), gaps, static Kiro border palette, `startup_apps` autostart, and Kiro
  `custom_actions` for the CTRL+ALT + SUPER+F1..F12 launchers.
- `default_action_overrides` move the SUPER+E "quit compositor" footgun to SUPER+Shift+E and map
  reload/floating/tabbing/stacking to the Kiro grammar. miracle's native tiling binds are kept.
- Shell = waybar (native `sway/workspaces`) + mako + swaybg, all shipped at their default config paths
  so a bare launch has no `~`-expansion dependency.
- Theming is a static Kiro palette; pywal deferred (miracle colours are YAML RGBA int-arrays).
- Lock/idle + screencast portal left as optional/unverified on Mir (documented in CLAUDE.md).

### Files Modified
- `etc/skel/.config/miracle-wm/{config.yaml,keybindings.txt,bg/kiro.jpg}`
- `etc/skel/.config/waybar/{config.jsonc,style.css}`
- `etc/skel/.config/mako/config`
- `README.md`, `CLAUDE.md`, `CHANGELOG.md`, scaffold (`up.sh`, `setup.sh`, `.gitignore`, `kiro.jpg`)
