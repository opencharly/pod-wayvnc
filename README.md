# pod-wayvnc

The `wayvnc` candy of the OpenCharly candy library, as a standalone repo (the
candy de-submodule cutover, kind-prefixed naming). It provides a WayVNC headless
VNC server exposing a wlroots Wayland desktop on TCP 5900.

## What it provides

Installs the `wayvnc` package (the `/usr/bin/wayvnc` server) plus a launcher
wrapper under `~/.local/bin`, run as a supervised service that publishes the
Wayland desktop over VNC on port 5900.

| Property | Value |
|---|---|
| Requires | `layer-supervisord` |
| Port | `tcp:5900` |
| Service | `wayvnc` (`~/.local/bin/wayvnc-wrapper`, `restart: always`, priority 20) |
| Install files | `wayvnc-wrapper` |
| Package | `wayvnc` (RPM) |

The wrapper uses a two-phase wait — the Wayland display socket, then the sway IPC
socket plus a short delay — so sway has set the output resolution before wayvnc
connects. On NVIDIA headless, the `sway-desktop-vnc` composition forces the
`pixman` renderer and the wrapper performs the DPMS workaround.

## How to use it

Part of the `sway-desktop` / `sway-desktop-vnc` composition:

```yaml
my-desktop:
  candy:
    - '@github.com/opencharly/pod-wayvnc:<tag>'
```

```bash
charly check live my-image --filter vnc
```

## Verification

The candy's `check:` plan asserts `/usr/bin/wayvnc`, the launcher wrapper, the
`wayvnc` package, and — at deploy scope — the running `wayvnc` service and a
reachable `127.0.0.1:${HOST_PORT:5900}`.

## Layout

- `charly.yml` — the `wayvnc:` candy entity (description, `require`, `port`,
  `distro`, `service`, `plan`) plus its `skill:` entity.
- `wayvnc-wrapper` — the launcher copied into `~/.local/bin`.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — this user overview.

## Related

- Owning skill: `/charly-selkies:wayvnc` — the candy properties, the NVIDIA
  headless fixes, and the startup timing.
- `/charly-selkies:sway` — the Wayland compositor providing the display.
- `/charly-selkies:sway-desktop-vnc` — the VNC composition that includes wayvnc.
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI and image builder
