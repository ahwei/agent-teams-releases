# Agent Teams

Local, **read-only** dashboard for monitoring [Claude Code](https://claude.ai/code) sessions
and the agent teams they spawn — live status, transcripts, token/cost, and tasks.
Everything is read straight from `~/.claude` on your own machine. Nothing is uploaded,
and the optional local browser mode binds to `127.0.0.1` only.

This repository hosts release binaries and the in-app auto-update feed.
The source code is in a separate repo (not publicly accessible).

📖 **New here?** The [**User Guide**](docs/MANUAL.md) walks through every screen with
screenshots (繁體中文).

![Agent Teams — Command Center](docs/images/01-command-center.png)

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

To upgrade later:

```bash
brew update && brew upgrade --cask agent-teams
```

### macOS / Windows — direct download

Grab the latest installer from the [Releases](https://github.com/ahwei/agent-teams-releases/releases) page:

| Platform | File |
|---|---|
| macOS Apple Silicon (M-series) | `Agent-Teams-<ver>-arm64.dmg` |
| macOS Intel | `Agent-Teams-<ver>-x64.dmg` |
| Windows x64 | `Agent-Teams-<ver>-x64.exe` (click through SmartScreen → "Run anyway") |

macOS direct-DMG installs need the same `xattr` quarantine step shown above.

## What it shows

- **Command Center** — every Claude Code session grouped by workspace: live/idle/
  stale/ended dot, working dir, spawned subagents, output tokens, cost, and last
  activity. Launch a saved team straight from here.
- **Teams** — design reusable agent-team blueprints as a role/handoff graph, see
  the native experimental teams, and start one into a live session.
- **Workflows** — the execution tree and per-stage status of any Claude Code
  workflow a session launches.
- **Transcripts** — three-panel viewer (sessions · agent tree · event stream).
  Click a subagent to read its own transcript. New lines stream in live.
- **Tokens & Cost** — per-session and per-model spend with charts. Prices come
  from a `pricing.json` that you can edit while it runs (hot-reloaded).
- **Tasks** — Pending / In Progress / Completed kanban with dependency edges.
- **Skills** — browse & install from [skills.sh](https://skills.sh), edit your
  local `SKILL.md` files, toggle or delete skills from the UI.
- **MCP Servers** — view configured MCP servers with live health (secret values
  stripped), with copy-ready `claude mcp add` commands.
- **Plugins** — manage installed plugins and marketplaces.
- **Settings** — edit `settings.json` (General toggles or Raw JSON) and the
  dashboard's own options in place.

See the [**User Guide**](docs/MANUAL.md) for a screen-by-screen walkthrough.

## Updates

- **Homebrew users**: `brew update && brew upgrade --cask agent-teams` (the `brew update` is required — otherwise the tap stays stale and brew reports "already on the latest")
- **In-app**: Help menu → "Check for Updates…" (Windows auto-checks on launch;
  macOS is opt-in via that menu item)

Both channels land on the same release version.

## How it works

A **Rust core** (Tauri v2) watches `~/.claude` on its own threads, normalizes
the files into an in-memory store, and pushes incremental updates to the React
UI over native IPC. The desktop app owns a single native window and webview — no
bundled Node server, no Electron. Transcripts are tailed by byte offset (never
fully re-read), and only the files of currently-known sessions are watched, so
the large `projects/` tree is never recursively scanned.

The optional browser mode is served by a small **axum** HTTP/WS server inside the
same Rust core, bound to `127.0.0.1` only.

Read more about Claude Code at <https://claude.ai/code>.

## License

[PolyForm Noncommercial License 1.0.0](LICENSE) — **non-commercial use only**.
Personal, educational, charitable, and government use is permitted, including
modification. Commercial use is prohibited.

See the full license at <https://polyformproject.org/licenses/noncommercial/1.0.0>
or in the installed app bundle at `/Applications/Agent Teams.app/Contents/Resources/LICENSE`.

This software is built with [Tauri](https://tauri.app) (MIT/Apache-2.0) and uses
your operating system's native webview (WKWebView on macOS, WebView2 on Windows).
