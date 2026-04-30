# Run autonomous heartbeats without Pi sessions

Autonomous heartbeat runs use the agent's `memory/HEARTBEAT.md` prompt but do not save Pi sessions. Heartbeat activity should be persisted deliberately into the agent's memory system, such as daily logs, `MEMORY.md`, `heartbeat-state.json`, world notes, and decisions, rather than accumulating opaque session transcripts. Interactive and normal one-shot runs may still use the agent's configured Pi session directory.
