# Local Autonomous Agents

This context describes a local system for running sandboxed autonomous Pi agents that can evolve independently without escaping their own filesystem boundary or modifying other agents.

## Language

**Agent System Home**:
A mutable, self-owned Unix-like home directory for one autonomous agent, mounted as `/home/agent` inside the sandbox.
_Avoid_: agent dir, profile dir, workspace when referring to the whole agent-owned tree

**Agent Harness Home**:
The Pi-specific home at `/home/agent/.config/pi/agent`, used as `PI_CODING_AGENT_DIR` and versioned as the main agent repository.
_Avoid_: system home, work directory

**Autonomous Heartbeat**:
A scheduled standalone agent run where the agent chooses and performs useful work without asking for approval.
_Avoid_: approval queue, manual trigger

**Agent Credential Set**:
The credentials mounted inside an **Agent Home** for model access, GitHub operations, signing, payments, and paid providers.
_Avoid_: host credentials, shared user credentials

**Agent Identity**:
The stable description of who an agent is.
_Avoid_: persona, behavior style

**Agent Persona**:
The stable description of how an agent behaves and presents itself, stored in `memory/PERSONA.md`.
_Avoid_: identity, SOUL.md

**Agent Memory System**:
The agent-owned knowledge system that stores identity, persona, world knowledge, relationships, events, lessons, decisions, and retrieval-oriented memory.
_Avoid_: chat history, session transcript

**Agent Harness Repository**:
The Git repository rooted at `/home/agent/.config/pi/agent` that versions an agent's durable Pi config, memory, world, decisions, skills, prompts, and extensions.
_Avoid_: including project repos, playgrounds, or private session logs

**Agent Session Repository**:
A private Git repository that versions Pi session history for one agent.
_Avoid_: mixing sessions into the main Agent Home repository

**Agent Skeleton**:
The trusted template tree used by `papa create` to scaffold a new Agent System Home before the interactive creation run populates agent-specific files.
_Avoid_: ad-hoc bootstrap files, generated defaults

**Agent Creation Run**:
An interactive Pi run started by `papa create` to collect user answers and populate a newly scaffolded agent's identity, persona, memory, and harness files.
_Avoid_: manual setup, init wizard when referring to the Pi-mediated run

**Birth Passport**:
The structured creation result written by an Agent Creation Run at `/home/agent/.config/pi/agent/birth-passport.json`, containing the minimal agent profile data needed to finish creating an Agent System Home.
_Avoid_: creation result, init metadata

## Relationships

- An **Agent System Home** belongs to exactly one autonomous agent and is mounted from `~/agents/<agent-name>` to `/home/agent`.
- An **Agent Harness Home** lives inside the **Agent System Home** and uses an XDG-style layout for Pi configuration, memory, world, and decisions.
- `papa` starts agents and is not owned by any agent.
- Every standalone agent run must pass through `papa` before tools or shell commands execute.
- An **Autonomous Heartbeat** runs one agent inside its sandbox using that agent's own memory, sessions, skills, extensions, and work directory.
- An **Agent Credential Set** is accessible to the owning agent inside its sandbox and is not treated as host-secret material.
- **Agent Identity** and **Agent Persona** are distinct: identity describes who the agent is; persona describes how the agent behaves.
- The **Agent Memory System** is separate from Pi sessions and is maintained across runs by the agent.
- `SYSTEM.md` anchors Pi startup and should refer the agent to its **Agent Memory System** rather than duplicate all memory content.
- An **Agent Harness Repository** is rooted at `/home/agent/.config/pi/agent`; `/home/agent` itself is not repository-managed.
- `work/` contains separate project repositories and `playgrounds/` remains disposable.
- An **Agent Session Repository** may store `sessions/` separately from the main **Agent Home Repository**.
- An **Agent Skeleton** is copied by `papa` into a new **Agent System Home** during `papa create`.
- An **Agent Creation Run** happens inside a temporary creation home after skeleton copy and before the new agent is considered ready for normal launch.
- A **Birth Passport** is written by the **Agent Creation Run** and may be used by Pi-assisted creation tooling to finish the new **Agent System Home**.
- `papa` exports explicit path variables with `_DIR` suffixes, such as `PI_AGENT_MEMORY_DIR`, `PI_AGENT_WORLD_DIR`, `PI_AGENT_SESSIONS_DIR`, and `PI_AGENT_DECISIONS_DIR`, for skills and extensions.

## Example dialogue

> **Dev:** "Can Bob modify the code that enforces Bob's sandbox?"
> **Domain expert:** "No. Bob owns his **Agent Home**"
