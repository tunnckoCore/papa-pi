# Local Autonomous Pi Agents Architecture

This document describes a local system for running standalone, autonomous, self-evolving Pi agents while keeping each agent contained to its own home directory.

## Goals

- Agents run standalone without a central orchestrator.
- Agents can be spawned from another Pi session as specialist subagents.
- Agents can run autonomously on a heartbeat schedule.
- Agents can manage their own Pi config, skills, extensions, prompts, memory, world wiki, decisions, sessions, and work projects.
- Agents cannot read or modify other agents' homes or the host filesystem.
- Agent-owned Pi extensions are allowed, so the entire Pi process must run inside the sandbox.

## Core decision

Each agent runs inside a whole-process Bubblewrap sandbox. The trusted launcher starts Pi inside the sandbox, with exactly one agent system home mounted as `/home/agent`.

```text
host
  ~/agents/bob  ──mounted writable──>  sandbox:/home/agent

not mounted:
  ~/agents/alice
  ~/agents/chandler
  host $HOME
  trusted launcher/runtime
```

Because Pi itself is inside Bubblewrap, any code loaded by Pi, including agent-owned extensions, is also contained.

## Agent filesystem

Sandbox layout mirrors host `~/agents/<name>` layout:

```text
~/agents/bob/
  .config/pi/sessions/
  .config/pi/agent/
    SYSTEM.md
    settings.json
    auth.json
    extensions/
    skills/
    prompts/
    memory/
      MEMORY.md
      PERSONA.md
      IDENTITY.md
      HEARTBEAT.md
      heartbeat-state.json
      daily-logs/
    world/
    decisions/
  work/
  playgrounds/
  tmp/
```

Inside the sandbox, the same tree is visible as:

```text
/home/agent/
  .config/pi/agent/
  .config/pi/sessions/
  work/
  playgrounds/
  tmp/
```

`/home/agent` is the agent's system-level home. `/home/agent/.config/pi/agent` is the Pi harness home and is used as `PI_CODING_AGENT_DIR`.

## Git repositories

The system home itself is not a Git repository.

```text
/home/agent/.config/pi/agent      -> owner/agent-{agent_username}
/home/agent/.config/pi/sessions   -> owner/sessions-{agent_username} private
/home/agent/work/*                -> separate project repositories
/home/agent/playgrounds/*         -> disposable local experiments
```

## Runtime environment

The trusted launcher sets:

```bash
HOME=/home/agent
PI_CODING_AGENT_DIR="$HOME/.config/pi/agent"
PI_AGENT_SESSIONS_DIR="$HOME/.config/pi/sessions"
PI_AGENT_MEMORY_DIR="$PI_CODING_AGENT_DIR/memory"
PI_AGENT_WORLD_DIR="$PI_CODING_AGENT_DIR/world"
PI_AGENT_DECISIONS_DIR="$PI_CODING_AGENT_DIR/decisions"
```

Pi is launched with:

```bash
pi --session-dir "$PI_AGENT_SESSIONS_DIR"
```

Nested agent launches should use the trusted launcher again rather than relying on shell aliases that rewrite `pi`.

## Execution modes

The same agent home supports three modes.

### Standalone interactive

```bash
papa bob
```

Starts Bob interactively inside the sandbox.

### One-shot or subagent run

```bash
papa bob -p "Review this design"
papa bob --json -p "Act as a specialist and return your findings"
```

A parent Pi session can call the trusted launcher to spawn a specialist agent. The specialist uses its own config, memory, extensions, skills, and sessions.

### Autonomous heartbeat

```bash
papa bob --heartbeat
```

Runs without saving a Pi session. The prompt comes from:

```text
$PI_CODING_AGENT_DIR/memory/HEARTBEAT.md
```

The agent persists durable traces into:

```text
$PI_CODING_AGENT_DIR/memory/daily-logs/YYYY-MM-DD.md
$PI_CODING_AGENT_DIR/memory/heartbeat-state.json
$PI_CODING_AGENT_DIR/memory/MEMORY.md
$PI_CODING_AGENT_DIR/world/
$PI_CODING_AGENT_DIR/decisions/
```

## Bubblewrap containment model

The launcher mounts the selected agent system home writable:

```bash
--bind ~/agents/bob /home/agent
--chdir /home/agent
```

It mounts system paths read-only as needed for tools to run. The launcher should auto-detect host layout and add read-only mounts conditionally, for example:

```bash
--share-net                                         # share host network
--ro-bind /usr /usr
--ro-bind /bin /bin
--ro-bind /lib /lib
--ro-bind /lib64 /lib64
--ro-bind /nix /nix                                 # when present
--ro-bind /run/current-system /run/current-system   # when present
--ro-bind /etc/ssl /etc/ssl
--ro-bind /etc/resolv.conf /etc/resolv.conf
--proc /proc
--dev /dev
--tmpfs /tmp
```

No other agent homes are mounted.

## Memory system

Memory is local-first. Each agent owns its own memory under:

```text
/home/agent/.config/pi/agent/memory
/home/agent/.config/pi/agent/world
/home/agent/.config/pi/agent/decisions
```

Canonical files:

```text
memory/IDENTITY.md                # who the agent is
memory/PERSONA.md                 # how the agent behaves
memory/MEMORY.md                  # long-term memory index and summary
memory/HEARTBEAT.md               # autonomous heartbeat prompt
memory/heartbeat-state.json       # heartbeat check timestamps
memory/daily-logs/YYYY-MM-DD.md   # no mental notes, actual things: text > brain
```

`world/` is the wiki-style knowledge base: people, agents, humans, projects, relationships, events, locations, lessons, preferences, and other world facts.

`decisions/` stores ADR-style records of important changes in the agent's identity, persona, memory, projects, career, or operating principles.

A future memory skill should provide query/write workflows over these files and may optionally sync with an external/global memory palace. The local files remain the base source of truth.

## Credentials

Credentials mounted inside the agent home are agent-owned capabilities. Examples:

- Pi `auth.json`
- GitHub account credentials for the agent collective
- SSH/signing keys
- P256 keys
- provider keys such as Venice.AI
- future payment/x402/ERC-8004 credentials

The sandbox protects the host and other agents from an agent. It does not protect the agent from misusing its own credentials.

## Open design areas

- Exact `papa` launcher implementation.
- Exact Bubblewrap mount matrix across distros.
- Heartbeat scheduler implementation.
- Subagent Pi extension API for calling `papa`.
- Memory skill command/API design.
- Agent-to-agent communication transport.
