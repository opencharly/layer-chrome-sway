# chrome-sway

Chrome autostart-on-Sway composition for OpenCharly desktop images.

The `chrome-sway` candy is a composition with one install of its own: a Sway
`config.d` drop-in (`chrome-sway.conf`) holding `exec chrome-wrapper`, so the
compositor launches Chrome on startup. It composes `sway` (the headless
compositor that reads `config.d`), `chrome` (the browser), and `chrome-cdp`
(which installs the `chrome-wrapper` launcher the snippet execs).

Chrome here is owned by Sway's `exec` directive, **not** a supervisord service:
it starts when Sway starts and does not auto-restart on exit. Relaunch it with a
`wl: exec` step running `chrome-wrapper` (do not use `charly shell` with a bare
`swaymsg` — the shell may lack the correct `SWAYSOCK` path).

## What it provides

| Property | Value |
|---|---|
| Layer / candy | `chrome-sway` |
| Requires | `pod-sway` |
| Composes | `layer-chrome`, `pod-chrome-cdp` |
| Installs | `chrome-sway.conf` → `${HOME}/.config/sway/config.d/chrome-sway.conf` (mode `0644`) |
| Autostart | `exec chrome-wrapper` |
| Service / port | none (Sway owns the Chrome process) |

## How to use it

Compose the candy by pinning this repo in a box's `candy:` list:

```yaml
my-browser:
  candy:
    base: cachyos
    candy:
      - '@github.com/opencharly/layer-chrome-sway:v2026.243.1820'
```

It is normally consumed through the `sway-desktop` composition. Once the desktop
is up, Chrome launches via `chrome-wrapper`; verify the installed drop-in and the
launcher:

```bash
cat ~/.config/sway/config.d/chrome-sway.conf     # → exec chrome-wrapper
ls -l ~/.local/bin/chrome-wrapper
```

## Layout

- `charly.yml` — the `chrome-sway:` candy entity: the `mkdir:`/`copy:` steps, the
  composed candies, and the `check:` assertions, plus the embedded `skill:`
  entity.
- `chrome-sway.conf` — the Sway `config.d` drop-in installed by the candy.
- `CHANGELOG/` — per-CalVer release notes.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — this user overview.

## Related

- Owning skill: `/charly-selkies:chrome-sway` — Chrome lifecycle in Sway, the
  `chrome-wrapper` autostart, and manual restart
- Browser layer: `/charly-selkies:chrome`; CDP: `/charly-check:cdp`
- Compositor: `/charly-selkies:sway`; desktop: `/charly-selkies:sway-desktop`
- VNC access: `/charly-selkies:wayvnc`
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI and image builder
- [`opencharly/opencharly`](https://github.com/opencharly/opencharly) — the umbrella
