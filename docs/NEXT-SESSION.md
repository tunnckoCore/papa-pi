# Next Session Handoff

Read these first:

1. [`CONTEXT.md`](../CONTEXT.md) — canonical language and resolved terms.
2. [`ARCHITECTURE.md`](../ARCHITECTURE.md) — accepted system architecture and scaffolds.
3. [`docs/adr/`](./adr/) — durable decisions and rationale.

## Next focus

Design the trusted `papa` launcher.

Start from the accepted model:

- `~/agents/<agent-name>` mounts to sandbox `/home/agent`.
- `PI_CODING_AGENT_DIR=/home/agent/.config/pi/agent`.
- `PI_AGENT_*_DIR` env vars point at memory/world/decisions/sessions.
- Pi starts with cwd `/home/agent`.
- Heartbeat uses `memory/HEARTBEAT.md` and runs without Pi sessions.

## Do not reopen unless necessary

- Launcher name: `papa`.
- Linux-first; macOS later.
- Whole-process Bubblewrap containment.
- XDG-style Agent System Home / Agent Harness Home split.
