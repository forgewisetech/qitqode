<h1 align="center">QitQode</h1>

<p align="center"><strong>Terminal AI coding agent with a memory.</strong></p>

<p align="center">
  <a href="https://qitqode.com">Website</a>
</p>

<p align="center">
  <strong>English</strong> | <a href="./README.zh.md">简体中文</a> | <a href="./README.zht.md">繁體中文</a> | <a href="./README.ja.md">日本語</a> | <a href="./README.fr.md">Français</a> | <a href="./README.ru.md">Русский</a> | <a href="./README.es.md">Español</a> | <a href="./README.pt.md">Português</a>
</p>

---

Most coding agents forget everything the moment a session ends. QitQode doesn't. It reads and writes code, runs commands, manages Git — and keeps a persistent, searchable memory of your project across sessions, rebuilding its own context when it runs long so it can keep working instead of starting over.

One account, one gateway, and the public Qortex capability set — **Free (no cost)**, **Fast**,
**Economy**, **Adaptive**, **Planner**, **Repair**, **Max intelligence**, and **Orchestrated**.
**Orchestrated** is a separate Qortex multi-stage pipeline capability (gate → plan → build → repair),
not an ordinary interactive selection. No provider dashboards, no API-key juggling, no per-model billing spreadsheets.

---

## Quick Start

```bash
npm install -g @qitqode/cli
# or: bun add --global @qitqode/cli

# Run
qitqode
```

The first launch guides you through sign-in:

- **Sign in with QitQode** — a device-code flow that works everywhere, including SSH sessions and remote sandboxes: the CLI shows a verification URL and code (and opens your browser when one is available); approve on any device to finish
- **API key** — paste a QitQode API key instead

Then pick a tier from the model selector and start working. That's the whole setup.

<details>
<summary><strong>WSL: clipboard issues</strong></summary>

If you encounter garbled text when copying on WSL, install `xsel`:

```bash
sudo apt install xsel
```

</details>

<details>
<summary><strong>Android (Termux)</strong></summary>

QitQode runs natively in Termux (no proot needed). The npm package ships an Android ARM64 runtime (`@qitqode/qitqode-android-arm64`) with the official Bun Android binary and a JS bundle; the `qitqode` launcher detects Termux via `TERMUX_VERSION`/`ANDROID_ROOT` and uses it automatically:

```bash
pkg install nodejs
npm install -g @qitqode/cli
qitqode
```

Notes for Termux:

- Requires `@qitqode/cli` ≥ 0.1.17.
- The interactive TUI's render library is built against musl and relies on Bionic's musl-compat symbols (`__errno_location`, added in Android 15 / API 35; `copy_file_range`, added in Android 14). On older Android versions the TUI may fail to start with a "cannot locate symbol" dlopen error (check the log dir under `~/.local/share/qitqode/log/`); headless `qitqode run` is unaffected.
- Keep Termux's default `TERM=xterm-256color`; no extra setup is needed.
- Sign-in uses the device-code flow: open the printed `https://accounts.forgewise.tech/device?code=…` URL in your Android browser and approve. (Termux may also offer to open it automatically via `termux-open-url` if `termux-api` is installed.)
- If `qitqode` reports the Android package is missing, reinstall with `npm install -g @qitqode/cli` — npm must be run inside Termux itself (not proot) so it picks the Android runtime package.

If the native path fails on your device, the previous `proot-distro` approach (Debian userland with `nodejs` + `npm install -g @qitqode/cli`) still works, and glibc userlands such as UserLAnd or Andronix run the regular Linux build directly.

</details>

---

## Why QitQode

You don't need another chat wrapper. You need an agent that can hold a long-running job without losing the plot. QitQode is built around four mechanisms:

### 1. Memory that survives the session

Every project gets a persistent memory layer backed by SQLite full-text search: project knowledge in `MEMORY.md`, automatic session checkpoints, scratch notes, and per-task progress logs. When you resume, the relevant memory is injected automatically — ranked and token-budgeted, not dumped. The agent picks up where it left off instead of relearning your codebase.

### 2. Context that rebuilds itself

Long tasks blow past context windows. QitQode watches the window, checkpoints state before it fills, and reconstructs working context from the latest checkpoint, project memory, and task progress — so a multi-hour refactor doesn't die at the token limit.

### 3. Autonomy you can hold accountable

Set a stopping condition with `/goal`. When the agent thinks it's done, an independent judge model reviews the conversation and decides whether the goal is actually met — no more optimistic "all done!" halfway through the job. Combine with the tree-shaped task tracker (`T1`, `T1.1`, …) and parallel subagents for real unattended work.

### 4. One subscription, zero provider plumbing

Seven interactive capabilities, one sign-in. Switch the selected capability mid-session with
`/free`, `/fast`, `/economical`, `/adaptive`, `/planner`, `/repair`, or `/max-int`. The
`/orchestrated` command selects Qortex's separate multi-stage pipeline (gate → plan → build → repair),
not one of the seven ordinary interactive tiers. Check your balance any time with the built-in qredits display — usage is
inspectable, not a surprise at month-end.

### And the parts others leave out

- **Credentials encrypted at rest** — your auth tokens are sealed with a key held in the OS keyring, and credential environment variables are stripped by default from every child process the agent spawns.
- **A TUI that everyone can use** — screen-reader accessible mode, `NO_COLOR` support, reduced motion, announcement verbosity control, and a WCAG AA high-contrast theme. First-class, not bolted on. (Details below.)
- **Plain MIT license** — no separate use-restrictions file, no service terms buried in the tail of the README.
- **Device-code sign-in built for real machines** — works over SSH, in containers, and in remote sandboxes where a loopback browser redirect never could.

---

## Core Features

### Multiple Agents

| Agent       | Description                                                                 |
| ----------- | --------------------------------------------------------------------------- |
| **build**   | Default. Full tool permissions for development                              |
| **plan**    | Read-only analysis mode for code exploration and solution design            |
| **compose** | Orchestration mode for specs-driven development and skill-driven workflows  |

Press `Alt+M` to cycle between primary agents. Subagents are created by the system as needed.

### Persistent Memory

Cross-session memory powered by SQLite FTS5 full-text search:

- **Project memory** (`MEMORY.md`) — persistent project knowledge, rules, and architecture decisions
- **Session checkpoint** (`checkpoint.md`) — structured state snapshots maintained automatically by the checkpoint-writer subagent
- **Scratch notes** (`notes.md`) — temporary note area for agents
- **Task progress** (`tasks/<id>/progress.md`) — per-task logs

Memory is injected automatically when a session resumes, so the agent does not need to relearn project context.

### Intelligent Context Management

- **Automatic checkpoints** — decides when to save session state based on the model context window
- **Context reconstruction** — when context approaches the limit, rebuilds it from the latest checkpoint, project memory, task progress, and retained recent messages so the agent can continue the current task
- **Budgeted injection** — uses a token budget to control how much checkpoint, memory, and notes content enters context, with importance ranking

### Task Tracking

A tree-shaped task system (`T1`, `T1.1`, `T1.2`, …) that integrates automatically with the checkpoint system, so task progress is preserved when sessions resume.

### Subagent System

The primary agent can create subagents on demand. Subagents share the current session context and can work in parallel, with lifecycle tracking, cancellation, and background execution.

### Goal / Stop Condition

The `/goal` command sets a stopping condition for a session. When the agent tries to stop, an independent judge model evaluates the conversation to decide whether the condition is truly satisfied — preventing premature "optimistic stops" during autonomous work.

### Compose Mode

Compose mode provides a structured workflow for specs-driven development. It includes built-in skills for planning, execution, code review, TDD, debugging, verification, and merging — orchestrating the full lifecycle from spec to shipped code.

### Prompt Prediction

Inline ghost-text suggestions predict your next prompt as you work — press `Tab` to accept.

### Deep Research

The built-in `/deep-research` workflow runs a structured multi-step investigation for questions that need more than a single search.

### Headless & IDE Use

Run `qitqode serve` for a headless HTTP server, or `qitqode acp` for Agent Client Protocol support to drive QitQode from compatible editors and remote environments.

**Unattended runs.** Three flags decide how often the TUI stops to ask you something:

| Flag | Questions | Tool permissions |
| --- | --- | --- |
| `--never-ask` | auto-decided | still prompt you |
| `--fullauto` | auto-decided | auto-approved, except what your config explicitly denies |
| `--headless` | auto-decided | auto-approved — and no TUI at all; needs `--prompt` or piped stdin |

`--fullauto` keeps the normal interactive TUI: you watch the session as it runs, and a `FULL-AUTO` badge stays on the prompt for as long as permissions are being granted on your behalf. Anything you set to `deny` in your config is still refused. `--fullauto` implies `--never-ask`, and passing it alongside `--headless` is harmless (headless already behaves this way).

### Voice Input

Real-time streaming voice input powered by TenVAD. Activate with `/voice`, then speak — audio is segmented by pauses and transcribed incrementally into the input. Requires `sox` (`brew install sox` on macOS, other platforms similar) and an explicitly configured speech-recognition model via the `voice` config field.

> **Note:** Coding models are tier-only through the QitQode backend. The `voice` field is a scoped exception used solely for speech recognition and voice control — it does not add models to the coding model list.

<details>
<summary><strong>WSLg audio setup</strong></summary>

```bash
sudo apt install -y sox pulseaudio libasound2-plugins
export PULSE_SERVER=unix:/mnt/wslg/PulseServer
```

</details>

<details>
<summary><strong>SSH remote audio (Mac → remote host)</strong></summary>

```bash
# Mac (local)
brew install pulseaudio
pulseaudio --load="module-native-protocol-tcp auth-ip-acl=127.0.0.1" --exit-idle-time=-1 --daemonize
# Add to ~/.ssh/config: RemoteForward 4713 127.0.0.1:4713

# Remote host
apt install -y pulseaudio pulseaudio-utils sox
export PULSE_SERVER=tcp:127.0.0.1:4713
# Verify: pactl info
```

</details>

### Dream & Distill

- **`/dream`** — scans recent session traces, extracts persistent knowledge into project memory, and removes outdated entries
- **`/distill`** — discovers repeated manual workflows in recent work and packages high-confidence candidates into reusable skills, subagents, or commands

---

## Configuration

QitQode is configured via `.qitqode/qitqode.json` in the project directory (or `~/.config/qitqode/qitqode.json` globally). Key options include:

- Qortex capability selection (Free (no cost), Fast, Economy, Adaptive, Planner, Repair, Max intelligence, and the
  Orchestrated pipeline)
- Agent permissions and custom agents
- Checkpoint and memory behavior
- MCP server connections
- Keybindings and theme

Max Mode (parallel best-of-N reasoning with judge selection) can be enabled via `experimental.maxMode` in the config.

---

## Accessibility

QitQode's TUI ships with first-class accessibility support:

- **Accessible mode** — set `QITQODE_TUI_ACCESSIBLE=1` (or `"tui": { "accessible": true }` in config) for a screen-reader friendly experience: linear main-screen rendering (no alternate screen), no mouse capture, low frame rate, reduced motion, and no sound cues.
- **NO_COLOR** — any non-empty [`NO_COLOR`](https://no-color.org) value switches to monochrome rendering with transparent backgrounds. Severity is never conveyed by color alone (toasts carry `ℹ ✓ ▲ ✗` symbols, diffs keep `+`/`-` markers).
- **Reduce motion** — set `QITQODE_REDUCE_MOTION=1` (or `"tui": { "reduce_motion": true }`) to replace spinners and animations with static text. Also toggleable at runtime from the command list.
- **Sound** — disable sound cues with `QITQODE_TUI_SOUND=0`, `"tui": { "sound": false }`, or the runtime toggle in the command list. Accessible mode always disables sound.
- **Announcement verbosity** — in accessible mode, control how chatty screen-reader announcements are with `QITQODE_TUI_ANNOUNCEMENTS=quiet|normal|verbose`, `"tui": { "announcements": "quiet" }`, or the runtime selector in the command list. `quiet` announces only turn boundaries; `normal` (default) adds tool-start lines; `verbose` adds tool-completion lines. Errors and aborts are always announced at every level.
- **High-contrast theme** — select the built-in `high-contrast` theme for pure black/white surfaces with WCAG AA-compliant colors.
- **Small terminals** — the TUI degrades gracefully in narrow terminals and shows a clear message when the window is below the 40x8 minimum.
- **Keyboard-only dialogs** — every dialog is fully operable without a mouse: `Esc` always closes, `Tab` (and arrow keys, where a dialog has a button row or list) moves focus, and `Enter` or `Space` activates the focused control. In plain/NO_COLOR terminals, the highlighted list row also carries a `›` marker so the selection is discernible without color.

Accessibility environment variables are one-way switches: `QITQODE_TUI_ACCESSIBLE=1`, `QITQODE_REDUCE_MOTION=1`, `NO_COLOR`, and `QITQODE_TUI_SOUND=0` always win over config values and runtime toggles — accessibility guarantees cannot be overridden back off. `QITQODE_TUI_ANNOUNCEMENTS` similarly wins over the config value and runtime selector when set.

---

## Development

```bash
bun install              # Install dependencies
bun run dev              # Run in development mode
bun turbo typecheck      # Type check
```

---

## License

Source code is licensed under the [MIT License](./LICENSE).
