# TUI end-to-end mock fixture

Drive a **live TUI session with zero credentials and zero network**. The
`preload.ts` here:

1. intercepts the qlex `streamTier` endpoint in `globalThis.fetch` and streams
   back a scripted scenario (raw qlex SSE: text tokens, OpenAI-shaped tool-call
   chunks, `qlex-stream` usage + `qlex-cost` summary frames, `[DONE]`),
2. starts the local QLEX protocol fixture (`../backend-server.ts`) and points
   `QITQODE_BACKEND_URL` at it so prompts/tiers/tool descriptions resolve
   without hitting the real QLEX data plane,
3. injects itself into the TUI's server **Worker thread** — bun `--preload`
   only runs on the main thread, but all model fetches happen in the Worker,
   so the preload wraps `globalThis.Worker` with a shim entry that imports the
   preload before the original worker module.

## One-command launch

```sh
qode/packages/qitqode/test/fixture/tui-mock/run.sh [project-dir]
```

Seeds a dummy API key into an isolated `QITQODE_HOME`
(`/tmp/qitqode-mock-home` by default), exports
`BUN_OPTIONS="--preload=.../preload.ts"`, and execs the TUI trusted against a
scratch project dir (`/tmp/qitqode-mock-playground` by default).

Type any prompt: turn 1 streams greeting text plus a scripted `bash` tool call
(`echo mock-tool-ok`); after the real tool executes, turn 2 streams the wrap-up
text. When turns run out, the last turn repeats.

## Scripted scenarios

Point `QITQODE_MOCK_SCENARIO` at your own JSON (see `scenario.default.json`):

```jsonc
{
  "delayMs": 30, // ms between SSE frames (simulated streaming)
  "turns": [
    {
      "events": [
        { "type": "text", "text": "..." },
        { "type": "tool_call", "name": "bash", "arguments": { "command": "..." } },
      ],
    },
  ],
}
```

Each POST to `streamTier` consumes the next turn. A turn containing any
`tool_call` event finishes with `finish_reason: "tool_calls"`, so the session
loop runs the (real) tool and calls the model again — which serves the next
turn.

## Driving it non-interactively (tmux)

Background daemons die between agent bash calls, so do everything in ONE
self-contained script:

```sh
tmux new-session -d -s mock -x 140 -y 40 \
  "qode/packages/qitqode/test/fixture/tui-mock/run.sh"
sleep 15                                    # startup
tmux send-keys -t mock "hello" Enter
sleep 25                                    # turn 1 + tool + turn 2
tmux capture-pane -t mock -p > /tmp/tui-mock-capture.txt
tmux kill-session -t mock
```

## Browser harness

The `artifacts/tui-terminal` harness spawns the CLI itself; to run it against
the mock, export the same env before starting the workflow (or spawn a tmux
session as above — the harness is not required for mocked runs):

```sh
export BUN_OPTIONS="--preload=$PWD/qode/packages/qitqode/test/fixture/tui-mock/preload.ts"
export QITQODE_MOCK_SCENARIO=...   # optional
```

and seed `$QITQODE_HOME/data/auth.json` as `run.sh` does (the harness uses
`QITQODE_HOME=/tmp/qitqode-web-home`).

## Gotchas

- `BUN_OPTIONS` must use the equals form (`--preload=/path`); the
  space-separated form breaks the CLI invocation.
- Invoke the CLI as `bun <file>` (direct), NOT `bun run <file>` — the `run`
  subcommand silently drops `BUN_OPTIONS` preloads.
- With the mock active a 401 can never wipe `auth.json`, but `run.sh` re-seeds
  the dummy credential on every launch anyway.
- `qitqode run` (headless) is single-process, so the same preload works there
  without the Worker shim.
