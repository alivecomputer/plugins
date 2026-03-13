<p align="center">
  <img src="alivecomputer-logo.png" alt="Alive Computer" width="600">
</p>

[![Version](https://img.shields.io/badge/version-1.0.1--beta-copper)](https://github.com/alivecomputer/claude-plugins/releases)
[![License: MIT](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)
[![Built for Claude Code](https://img.shields.io/badge/built%20for-Claude%20Code-blueviolet)](https://docs.anthropic.com/en/docs/claude-code)

# ALIVE

**Personal Private Context Infrastructure**

Your context is your property. ALIVE turns your local file system into a structured, persistent world that any AI agent can read, work in, and hand off — without cloud dependency, vendor lock, or data leaving your machine.

Plain markdown files. A caretaker runtime. 12 skills. Your computer, alive.

## Install

```bash
claude plugin install alive@alivecomputer
```

Then start a session and type `/alive:world`.

## What You Get

- **12 skills** for managing your world
- **6 rules** that define caretaker behavior
- **12 hooks** for session lifecycle, log protection, archive enforcement, stash preservation
- **Templates** for walnuts, capsules, companions, and system files
- **Onboarding** that scaffolds your world on first run

## Skills

| Skill | Command | Purpose |
|-------|---------|---------|
| World | `/alive:world` | Dashboard — see everything, route to action |
| Load | `/alive:load` | Load a walnut — brief pack, people, capsule context |
| Save | `/alive:save` | Checkpoint — route stash, update state, keep working |
| Capture | `/alive:capture` | Bring external content in — store, route, extract |
| Find | `/alive:find` | Search across all walnuts — decisions, people, files |
| Create | `/alive:create` | Scaffold a new walnut with full structure |
| Tidy | `/alive:tidy` | System maintenance — stale drafts, orphan files, unsigned sessions |
| Tune | `/alive:tune` | Customize voice, rhythm, and preferences |
| History | `/alive:history` | Session recall — what happened, when, why |
| Mine | `/alive:mine` | Deep extraction from source material |
| Extend | `/alive:extend` | Create custom skills, rules, and hooks |
| Map | `/alive:map` | Interactive force-directed graph of your world |

---

## Architecture

### The Runtime Injection Pattern

ALIVE doesn't fine-tune a model or depend on a specific AI provider. It injects a **caretaker runtime** — a portable set of rules, skills, and hooks — into whatever agent starts a session.

```
Session starts
  → session-new hook fires
  → injects squirrel.core@1.0 via additionalContext
  → any model becomes a squirrel
  → reads the walnut's core files
  → resumes exactly where the last session left off
```

The runtime is the role. The model is the engine. Swap Claude for GPT, Gemini, or a local model — the squirrel still knows how to read a walnut, stash context, and save cleanly. The context belongs to the file system, not the model's memory.

### Closed-Loop Session Model

Every session follows the same lifecycle. No unclosed loops. No orphaned state.

```
OPEN ──→ WORK ──→ SAVE ──→ (continue or exit)
  │         │         │
  │         │         ├─ stash routed to files
  │         │         ├─ log entry prepended (signed)
  │         │         ├─ now.md regenerated from scratch
  │         │         └─ squirrel YAML signed
  │         │
  │         ├─ stash accumulates in conversation (not files)
  │         ├─ capture writes raw to capsules immediately
  │         └─ checkpoint every 5 items or 20 min (crash insurance)
  │
  ├─ core files read in sequence
  ├─ previous stash recovered if unsigned
  └─ one observation surfaced before work begins
```

If a session crashes, the next one recovers. The stash checkpoint in the squirrel YAML means nothing is lost. The unsigned entry detection means nothing is silently dropped. Every write is signed with session ID, runtime version, and engine.

### The Capsule Workflow

Capsules model how work actually happens with AI — you prototype, iterate, ship, and the context compounds.

```
CAPTURE ──→ DRAFT ──→ PROTOTYPE ──→ PUBLISHED ──→ DONE
  │            │           │             │            │
  raw/         v0.1.md     v0.2.md       v0.3.md      v1.md
  sources      markdown    + visual      shared       graduated
                           (HTML)        externally   to walnut root
```

Each capsule is self-contained: a companion index, versioned drafts, and raw source material. The companion tracks goal, status, sources, changelog, and work log. Multiple agents can work on different capsules concurrently — active session claims prevent collisions.

Capsules pull from captured context. They contribute back to the walnut's log, insights, and tasks. Nothing exists in isolation.

When a capsule ships v1, it graduates from the workshop (`_core/_capsules/`) to the walnut root — becoming live context alongside the work it produced.

### Context Revival

The zero-context standard means any agent can pick up any walnut cold:

1. **key.md** — what this is, who's involved, how it connects
2. **now.md** — current phase, active capsule, next action
3. **tasks.md** — prioritized work queue with attribution
4. **insights.md** — standing domain knowledge (confirmed evergreen)
5. **log.md** — full history, newest first, every entry signed

No briefing doc. No onboarding call. The files ARE the context. Read them in order and you're caught up. This is what makes the runtime portable — the walnut doesn't need the same model, the same session, or even the same AI platform to continue.

### Hook Pipeline

12 hooks enforce system guarantees mechanically — not by asking the agent to follow rules, but by blocking violations before they happen.

| Hook | Trigger | Guarantee |
|------|---------|-----------|
| session-new | Session start | Runtime injected, squirrel entry created |
| session-resume | Session resume | Previous stash recovered |
| session-compact | Context compaction | Stash preserved across memory compression |
| log-guardian | Edit/Write to log.md | Signed entries are immutable |
| rules-guardian | Edit/Write to plugin files | System files can't be accidentally modified |
| archive-enforcer | Bash rm/rmdir | Nothing gets deleted — only archived |
| external-guard | Any MCP write tool | External actions require explicit confirmation |
| pre-compact | Before compaction | Timestamp recorded for session continuity |
| post-write | After file edit | Edit count tracked, statusline updated |
| inbox-check | After writing now.md | Surfaces unrouted items in Inputs |
| root-guardian | Edit/Write to world root | Non-ALIVE files blocked at root, routed to walnut |
| context-watch | Every user prompt | Context usage monitored, save nudges at thresholds |

The hooks are the hard guardrails. The rules are the soft guidance. Together they create a system where the agent can work fast without breaking things.

---

## How It Works

### The Walnut

A walnut is the unit of context. Any meaningful thing with its own identity, lifecycle, and history.

```
my-project/
  _core/
    key.md              identity — people, rhythm, tags, connections
    now.md              state — phase, next action, active capsule
    log.md              history — prepend-only, signed entries
    insights.md         knowledge — confirmed evergreen facts
    tasks.md            work — prioritized queue with attribution
    _squirrels/         session entries (one YAML per session)
    _capsules/          the workshop
      website-rebuild/
        companion.md    index — goal, status, sources, changelog
        v0.1.md         working draft
        v0.2.md         iterated
        raw/            source material
  website-rebuild/      graduated capsule (shipped, lives at root)
  docs/                 live context — your actual work
```

### The ALIVE Domains

Five folders. The letters are the framework.

```
01_Archive/       Everything that was. Mirror paths. Graduation, not death.
02_Life/          Personal. Goals, people, patterns. The foundation.
03_Inputs/        Buffer only. Content arrives, gets routed out.
04_Ventures/      Revenue intent. Businesses, clients, products.
05_Experiments/   Testing grounds. Ideas, prototypes, explorations.
```

### The Squirrel

The caretaker runtime. Not a chatbot personality — a portable set of behaviors that any model inherits when it enters a session.

**Instincts** (always running):
- Read before speaking — never answer from memory
- Capture proactively — external content enters the system or dies with the session
- Surface connections — cross-walnut references, people mentions, stale context
- Flag age — warn when context is older than the walnut's rhythm expects

**The stash** is the in-session scratchpad. Decisions, tasks, notes, insight candidates, and quotes accumulate during work. Nothing writes to walnut files mid-session (except capture and capsule drafts). At save, the stash routes to the right files — log, tasks, insights, cross-walnut dispatches.

## Core Principles

- **Context as property.** Your files, your machine, your cloud. Nothing phones home.
- **Zero-context handoff.** Any new agent picks up any walnut cold and continues.
- **Surface, don't decide.** The squirrel shows what it found. You choose what stays.
- **Capture before it's lost.** What lives only in conversation dies with the session.
- **No unclosed loops.** Every session opens cleanly, works tracked, saves completely.

## Background

ALIVE was built in a lab — hundreds of hours of agent sessions across real ventures, testing context persistence, agent handoff, and the limits of what AI can reliably manage unsupervised.

### What We Learned About Safety

Agents without structural guardrails will:
- **Overwrite state** — editing files they shouldn't, silently replacing context from previous sessions
- **Perform irreversible actions** — deleting files, force-pushing branches, sending messages without confirmation
- **Leak sensitive data** — writing API keys into committed files, hardcoding paths with personal information
- **Drop context silently** — losing stash items to session crashes, forgetting the previous `next:` action, conflating walnut scopes
- **Fabricate confidence** — answering from "memory" instead of reading the source of truth

Every hook in the system exists because one of these things happened. The log guardian makes signed entries immutable. The archive enforcer blocks deletion — you archive, you don't destroy. The external guard gates any action that touches systems outside your machine (email, GitHub, APIs) behind explicit confirmation. The rules guardian prevents the agent from modifying its own runtime. These aren't theoretical — they're scar tissue from real failures.

### What We Learned About Context

The capsule architecture came from observing how work actually flows with AI: you capture raw material, draft something, iterate with feedback, ship it, and the context compounds into the next thing. The system models that workflow structurally rather than hoping the agent remembers it.

The zero-context standard — the requirement that any new agent can pick up any walnut cold — forced every design decision toward explicit, file-based state. No hidden memory. No session-dependent knowledge. If it's not in the files, it doesn't exist.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

## Community

- [Worldbuilders on Skool](https://skool.com/worldbuilders) — discussion, feedback, show & tell
- [GitHub Discussions](https://github.com/alivecomputer/claude-plugins/discussions) — bugs, features, ideas

## License

MIT. Open source. Build your world.
