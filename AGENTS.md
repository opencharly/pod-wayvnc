# AGENTS.md — pod-wayvnc

Standalone candy repo for the `wayvnc` candy — a WayVNC headless VNC server
exposing a wlroots Wayland desktop on TCP 5900. The candy lives in `charly.yml`
at the repo root plus its launcher.

Canonical files:

- `charly.yml` — the `wayvnc:` candy entity (description, `require`, `port`,
  `distro`, `service`, `plan`) and its `skill:` entity.
- `wayvnc-wrapper` — the launcher copied into `~/.local/bin`.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `CHANGELOG/` — per-CalVer history.
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-selkies:wayvnc` — the owning skill: the candy properties, the NVIDIA
  headless fixes, and the startup timing. Load before editing, building,
  deploying, or troubleshooting this candy.
- `/charly-selkies:sway` — the Wayland compositor providing the display.
- `/charly-selkies:sway-desktop-vnc` — the VNC composition that includes wayvnc.
- `/charly-check:vnc` — the `vnc:` check verb (screenshot, click, type).
- `/charly-pod:pod` — the `kind: pod` / deploy schema reference (this candy is
  composed into a box; services).
- `/charly-image:layer` — the candy authoring reference (`charly.yml` schema,
  `plan:` step verbs incl. `copy:` / `check:`, service declarations).
- `/charly-check:check` — the check/R10 framework (`charly check box`,
  `charly check run <bed>`).
- `/charly-internals:git-workflow` — before any git/PR action.

## Build / validate / test

- `charly box validate` at the repo root — the structural check: the manifest
  must parse and validate at the installed charly.
- The merge gate is the **org-wide** `charly/pr-validator` (required check
  `validate / validate`, defined in `opencharly/.github`); this repo has **no**
  per-repo candy gate. Its only workflow file is
  `.github/workflows/tag-on-merge.yml`.
- The candy's own `check:` steps assert `/usr/bin/wayvnc`, the launcher wrapper,
  the `wayvnc` package, and — at deploy scope — the running service and a
  reachable published port.

## Modify this repo

- Edit the `wayvnc:` candy entity in `charly.yml`; the `skill:` entity in the
  same file is the owning skill's source — a candy change and its skill change
  land together.
- Keep the wrapper's two-phase wait (Wayland socket, then sway IPC socket +
  delay) so sway has set the output resolution before wayvnc connects.
- The VNC port is `tcp:5900` (non-HTTP); keep the `port:` entry and the
  `addr:` runtime checks in step.
- The `skill:` entity is the source for `/charly-selkies:wayvnc`; never edit the
  generated `SKILL.md` — regenerate it.

## Landing

- PR-only. Every change lands through a pull request; the org-required
  `charly/pr-validator` validates the diff and body and arms native auto-merge on
  PASS. Direct pushes to `main` are blocked.
- History lives in `CHANGELOG/` (written by `tag-on-merge` at merge time); the PR
  body IS the changelog.
- The authoritative rulebook is the umbrella `AGENTS.md` in
  `opencharly/opencharly` and `charly/AGENTS.md` in the charly repo. Do not
  restate its rules here.
