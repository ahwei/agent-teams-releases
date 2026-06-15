# Agent Teams — User Guide

> 🌐 **Language**: **English** · [繁體中文](MANUAL.zh-TW.md)

Agent Teams is a **read-only** desktop dashboard that runs on your own machine and
watches your [Claude Code](https://claude.ai/code) sessions and the agent teams they
spawn — status, transcripts, tokens/cost, task boards, and more. Everything is read
straight from your local `~/.claude` directory; **nothing is uploaded**. Even in the
optional browser mode the built-in server binds to `127.0.0.1` (loopback) only, so no
other device can reach it.

> Under the hood it is a **Tauri v2** app: a **Rust core** watches the filesystem and a
> React UI renders inside a native window, talking over native IPC. It is **not** a
> web/cloud service, and it no longer ships a Node/Express server or an Electron shell.

This guide walks through each screen. For installation, see the [README](../README.md).

---

## Contents

- [Interface overview](#interface-overview)
- [1. Command Center](#1-command-center)
- [2. Teams](#2-teams)
- [3. Workflows](#3-workflows)
- [4. Transcripts](#4-transcripts)
- [5. Tokens & Cost](#5-tokens--cost)
- [6. Tasks](#6-tasks)
- [7. Skills](#7-skills)
- [8. MCP Servers](#8-mcp-servers)
- [9. Plugins](#9-plugins)
- [10. Settings](#10-settings)
- [Running it in a browser (local web mode)](#running-it-in-a-browser-local-web-mode)
- [Sending prompts from the dashboard (optional, off by default)](#sending-prompts-from-the-dashboard-optional-off-by-default)
- [Privacy & security](#privacy--security)

---

## Interface overview

The left sidebar is a fixed nav split into three groups:

- **Top (live / operate)**: Command Center, Teams, Workflows — the work you have in flight.
- **Analytics (cross-session rollups)**: Transcripts, Tokens & Cost, Tasks.
- **Workspace (config, collapsible)**: Skills, MCP Servers, Plugins, Settings.

The green dot and `live` label in the top-right show the connection between the
dashboard and the local core (`live` / `connecting` / `disconnected`).

---

## 1. Command Center

![Command Center](images/01-command-center.png)

The first screen you land on. It groups every Claude Code session by **workspace**
(working directory). Each session shows:

- **Liveness**: `live` / `stale` (idle for a while) / `ended` dots and labels.
- A **session short-code** or a title derived from the first prompt (e.g. "Fix
  copy-paste bug in packaged Electron app" above).
- The number of **spawned subagents** (e.g. `11`), cumulative **output tokens** and
  **cost**, and the last activity time.

The **Run a team** button in the top-right launches a saved team blueprint right here.

---

## 2. Teams

![Teams](images/02-teams.png)

Manage native agent teams — several independent Claude instances with a lead that
coordinates them; teammates message each other directly and share one task list. The
view has several tabs:

- **Active teams**: native teams currently running (requires
  `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`).
- **Blueprints**: reusable team designs you can launch repeatedly. The `product-squad`
  above is shown as a **graph** of roles and handoffs (7 roles · 8 handoffs):
  product-manager → architect → designer / backend-engineer / frontend-engineer →
  qa-engineer → code-reviewer. Use **Start in live session**, **Parallel subagents**, or
  **Open in Builder** to act on it.
- **Roles**: the individual role definitions used by a blueprint.
- **Guide**: step-by-step instructions for enabling and building a team.

---

## 3. Workflows

![Workflows](images/03-workflows.png)

Shows Claude Code workflow runs. As soon as a session launches a workflow, its execution
tree and per-stage status appear here live; until then you see the empty-state hint
(above).

---

## 4. Transcripts

![Transcripts](images/04-transcripts.png)

A three-panel viewer: pick a session on the left, the middle is the **agent tree**
(`Main session` plus every subagent), and the right is the event stream. Above, a
`product-squad` run with **11 subagents** is open, and the `designer` subagent is
selected — so the right panel shows **that subagent's own transcript** (design spec,
relevant files, code snippets).

- Each tool call (Bash, edits, etc.) is an expandable row labeled `Running` /
  `Completed` / `Error`.
- For a running session, new content streams in live.

---

## 5. Tokens & Cost

![Tokens & Cost](images/05-cost.png)

Per-session and per-model usage and spend, as charts:

- Four cards up top: **total cost**, **input / output tokens**, **cache-read tokens**.
- **Cost by session** and **Cost by model** bar charts, plus a **Per session** table at
  the bottom.
- Prices come from `pricing.json` (USD per 1,000,000 tokens). Drop your own at
  `~/.claude/pricing.json` to override it — the app **hot-reloads** and re-costs every
  session live, no restart. Models not in the table are priced at the default rate (with
  a notice).

---

## 6. Tasks

![Tasks](images/06-tasks.png)

A **Pending / In Progress / Completed** kanban for a session's tasks, with per-session
tabs at the top. Each card shows the task number, title, description, and **dependency**
badges (e.g. `blocked by #3`), so you can see at a glance which tasks are waiting on a
prerequisite.

---

## 7. Skills

![Skills](images/07-skills.png)

Browse and manage your Claude Code skills. You can:

- Browse and install from [skills.sh](https://skills.sh) (**Browse skills**, top-right).
- **Enable / disable** a skill's model auto-invocation with a toggle.
- Edit a local `SKILL.md` in place, or delete a skill.
- Switch between **User-global** (`~/.claude/skills`) and per-project
  (`<cwd>/.claude/skills`) skills via tabs.

---

## 8. MCP Servers

![MCP Servers](images/08-mcp.png)

View the MCP servers configured in `~/.claude.json` and per-project `.mcp.json`. Each
card shows the name, transport (SSE / HTTP…), and **live health** (`OK` / `Needs auth`).

- Provides copy-ready commands such as **Copy `claude mcp add`**.
- **Security note**: API keys, bearer tokens, and env **values** are stripped at the
  server — only key names reach the UI, so secrets never leak.

---

## 9. Plugins

![Plugins](images/09-plugins.png)

Manage installed plugins and marketplaces. A grid lists each plugin (name, source,
scope) with enable/disable toggles; you can install by id from the top input, and drill
in to see a plugin's component inventory and projected token cost.

---

## 10. Settings

![Settings](images/10-settings.png)

Two sections:

- **Dashboard**: the dashboard's own toggles, including **Enable web runs & live
  injection** (let the chat box run `claude` headlessly with `--resume` or inject into a
  live session — **off by default**) and **Allow write/exec permission modes**. See below.
- **Claude Settings**: edit `~/.claude/settings.json`, `settings.local.json`, or an open
  project's `.claude/settings.json` directly. The **General** tab covers common toggles
  (Effort level, Always Thinking, Auto Memory…); everything else is on the **Raw JSON**
  tab. Saves are validated — broken JSON is refused.

---

## Running it in a browser (local web mode)

Besides the desktop window, the same dashboard can run in a normal browser tab — same
data, same features — served by a small HTTP/WS server built into the Rust core. It
binds to **`127.0.0.1` only** and rejects non-loopback `Host`/`Origin` requests, so a
malicious web page can't reach it either.

- **From the installed desktop app (no terminal)**: open **Settings → Dashboard →
  "Serve dashboard to browser"**. It starts a loopback server right away and shows the
  URL; the choice is remembered.
- Advanced users can also start a headless mode from a terminal (see the source project).

The web version has full feature parity with the desktop app, including the same opt-in,
off-by-default write paths — nothing is loosened for the browser.

---

## Sending prompts from the dashboard (optional, off by default)

By default the app is fully read-only. You can opt in to let the chat box type **straight
into a running session** — the text appears in your real terminal, as if you typed it.
The backend is auto-detected (the UI is identical either way):

- **iTerm2**: driven via AppleScript by matching the session's tty. No tmux needed.
- **Terminal.app**: macOS's built-in terminal, also driven via AppleScript.
- **tmux**: `tmux send-keys` into the session's pane — works in **any** terminal
  (including VS Code's) as long as you started `claude` inside tmux.

Enable it under **Settings → Dashboard → "Enable web runs & live injection"**. The choice
is saved to `~/.claude/agent-teams-dashboard.json`, so it works on a normal double-click.

---

## Privacy & security

- **Read-only by principle**: the core only reads `~/.claude`, with a handful of
  **explicit, off-by-default** write paths (editing skills / agents / team blueprints /
  settings, and — off by default — running `claude` headlessly or injecting into a live
  session). It never touches your sessions, transcripts, or tasks otherwise.
- **No upload**: all computation is local; browser mode is `127.0.0.1`-only, with no
  remote/LAN access and no login.
- **Secrets stripped**: MCP env/header values, IDE lock tokens, and the like are dropped
  at the scan boundary and never appear in the UI or any log.

---

_Screenshots in this guide use demonstration data. Your actual screens reflect the
sessions and configuration in your own `~/.claude`._
