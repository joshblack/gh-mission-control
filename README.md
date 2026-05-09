# gh-mission-control

A **terminal session manager for AI coding agents** — a `gh` extension written in Rust.

`gh mission-control` gives you a beautiful TUI to manage all your [GitHub Copilot](https://github.com/features/copilot) agent sessions at once. It reads directly from the Copilot CLI's session store (`~/.copilot/session-state/`) so every session you've ever started automatically appears here — no setup required.

---

## Features

- **Reads real Copilot CLI sessions** from `~/.copilot/session-state/` — no extra configuration
- **Split-pane TUI** — sessions list on the left, session detail/conversation on the right
- **Sessions grouped by working directory** and sorted newest-first
- **Smart status polling** with Running (`●` green), Waiting (`◐` yellow), Idle (`○` gray), and Error (`✕` red) indicators
- **Conversation history** — view user messages and Copilot responses in the detail pane
- **Vim-style navigation** — `j`/`k` to move, `Enter`/`Space` to view detail
- **Launch new sessions** — `n` to start `copilot -C <dir>` with the current directory pre-filled
- **Resume sessions** — `o` to resume any existing session with `copilot --resume=<id>`
- **Embedded Copilot terminal** — resumed and newly launched sessions run in tmux-backed panes
- **Reload** — `r` to refresh from disk at any time
- **Shortcut help** — `?` shows a scrollable shortcut reference

---

## Installation

### As a `gh` extension

```sh
gh extension install joshblack/gh-mission-control
```

Then run:

```sh
gh mission-control
```

### Build from source

```sh
git clone https://github.com/joshblack/gh-mission-control
cd gh-mission-control
cargo build --release
./target/release/gh-mission-control
```

---

## Requirements

- [GitHub Copilot CLI](https://docs.github.com/copilot/how-tos/copilot-cli) — install with `gh copilot`
- [tmux](https://github.com/tmux/tmux/wiki) — used to keep embedded Copilot sessions alive after detach
- Sessions are stored in `~/.copilot/session-state/` (created automatically when you run `copilot`)

---

## Key Bindings

### Sessions panel (left)

| Key | Action |
|-----|--------|
| `j` / `↓` | Move selection down |
| `k` / `↑` | Move selection up |
| `Enter` / `Space` | View session detail |
| `o` | Open/resume in Copilot |
| `n` | Launch new Copilot session |
| `r` | Reload sessions from disk |
| `?` | Show shortcut help |
| `q` | Quit |

### Detail panel (right)

| Key | Action |
|-----|--------|
| `j` / `↓` | Scroll conversation down |
| `k` / `↑` | Scroll conversation up |
| `o` | Open/resume in Copilot |
| `Esc` / `h` / `←` | Return to sessions list |
| `n` | Launch new Copilot session |
| `?` | Show shortcut help |
| `q` | Quit |

### New session prompt

| Key | Action |
|-----|--------|
| `Enter` | Launch `copilot -C <dir>` |
| `Esc` | Cancel |
| Type | Edit the directory path |

### Shortcut help

| Key | Action |
|-----|--------|
| `j` / `↓` / scroll | Scroll down |
| `k` / `↑` / scroll | Scroll up |
| `PageDown` / `PageUp` | Scroll by page |
| `Esc` / `q` / `?` | Close |

### Embedded terminal

| Key | Action |
|-----|--------|
| `Ctrl+F` | Toggle fullscreen |
| `Ctrl+W` | Detach from the embedded terminal |
| Other keys | Forward input to Copilot |

Embedded terminals are tmux clients. Detaching closes only the in-app terminal view; the backing Copilot process keeps running in its tmux session so it can be resumed later.

---

## Session storage

Sessions are stored by the Copilot CLI itself. `gh-mission-control` reads from:

- **Session metadata**: `~/.copilot/session-state/<id>/workspace.yaml`
- **Conversation history**: `~/.copilot/session-store.db` (SQLite)

### `workspace.yaml` structure

```yaml
id: <uuid>
cwd: /path/to/project
git_root: /path/to/project
repository: owner/repo
branch: feature/my-feature
user_named: false
created_at: 2024-01-15T10:30:00Z
updated_at: 2024-01-15T11:45:00Z
```

### Launching sessions from the CLI

You can also launch sessions directly with the Copilot CLI:

```sh
# Start a new session in the current directory
copilot

# Start a named session
copilot --name="my feature"

# Resume a previous session
copilot --resume=<session-id>
```
