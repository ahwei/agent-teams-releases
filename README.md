# Agent Teams

Local, **read-only** dashboard for monitoring [Claude Code](https://claude.ai/code) sessions
and the agent teams they spawn — live status, transcripts, token/cost, and tasks.
Everything is read straight from `~/.claude` on your own machine. Nothing is uploaded;
the embedded server binds to `127.0.0.1` only.

This repository hosts release binaries and the in-app auto-update feed.
The source code is in a separate repo (not publicly accessible).

## Install

### macOS — Homebrew (recommended)

```bash
brew tap ahwei/tap
brew install --cask agent-teams
```

The app is **ad-hoc signed** (no Apple Developer ID, no notarization), so first
launch is blocked by Gatekeeper. Allow it once with:

```bash
xattr -dr com.apple.quarantine "/Applications/Agent Teams.app"
```

Or right-click the app in `/Applications` → **Open** → confirm.

### macOS / Windows — direct download

Grab the latest installer from the [Releases](https://github.com/ahwei/agent-teams-releases/releases) page:

| Platform | File |
|---|---|
| macOS Apple Silicon (M-series) | `Agent-Teams-<ver>-arm64.dmg` |
| macOS Intel | `Agent-Teams-<ver>-x64.dmg` |
| Windows x64 | `Agent-Teams-<ver>-x64.exe` (click through SmartScreen → "Run anyway") |

macOS direct-DMG installs need the same `xattr` quarantine step shown above.

## What it shows

- **Status** — every running Claude Code session as a card: live/idle/stale/ended
  dot, working dir, version, last activity, spawned subagents, and a one-click
  `claude --resume` command.
- **Transcripts** — three-panel viewer (sessions · agent tree · event stream).
  Click a subagent to read its own transcript. New lines stream in live.
- **Tokens & Cost** — per-session and per-model spend with charts. Prices come
  from a `pricing.json` that you can edit while it runs (hot-reloaded).
- **Tasks** — Pending / In Progress / Completed kanban with dependency edges.
- **Skills** — browse & install from [skills.sh](https://skills.sh), edit your
  local `SKILL.md` files, delete skills directly from the UI.
- **Agents / Teams** — manage subagent role definitions and team blueprints
  (`agents/*.md`, `agent-teams/<slug>.team.json`).

## Updates

- **Homebrew users**: `brew update && brew upgrade --cask agent-teams` (the `brew update` is required — otherwise the tap stays stale and brew reports "already on the latest")
- **In-app**: Help menu → "Check for Updates…" (Windows auto-checks on launch;
  macOS is opt-in via that menu item)

Both channels land on the same release version.

## How it works

A small Node server (Express + ws + chokidar) reads `~/.claude` and pushes
incremental updates to a React SPA over a WebSocket. Wrapped in Electron for
the desktop app shape — the server lives in-process inside the main process,
no `child_process` overhead, single PID lifecycle.

Read more about Claude Code at <https://claude.ai/code>.

## License

[PolyForm Noncommercial License 1.0.0](LICENSE) — **non-commercial use only**.
Personal, educational, charitable, and government use is permitted, including
modification. Commercial use is prohibited.

See the full license at <https://polyformproject.org/licenses/noncommercial/1.0.0>
or in the installed app bundle at `/Applications/Agent Teams.app/Contents/Resources/LICENSE`.

This software bundles [Electron](https://www.electronjs.org) (MIT-licensed).
