# Customer Simulation Skills for Cursor

A Cursor plugin that distributes skills for running customer simulations and acting on the results via [The Simulation Company](https://thesimulationco.com) MCP.

## Install

The plugin has two pieces — a **skill** (`simulation-testing`) that contains the simulation workflow, and a **rule** (`simulation-standing-order`) that's always-on so the agent reaches for the skill at the right moment.

You can install them either **per-project** (only this repo gets simulations) or **globally** (every Cursor project on this machine).

### Per-project install

#### 1. Install the skill

```bash
mkdir -p .cursor/skills && \
  curl -sSL https://github.com/the-simulation-company/simco-cursor-plugin/archive/refs/heads/main.tar.gz \
  | tar -xz --strip-components=3 -C .cursor/skills 'simco-cursor-plugin-main/.cursor/skills'
```

#### 2. Install the standing-order rule

```bash
mkdir -p .cursor/rules && \
  curl -sSL -o .cursor/rules/simulation-standing-order.mdc \
  https://raw.githubusercontent.com/the-simulation-company/simco-cursor-plugin/main/.cursor/rules/simulation-standing-order.mdc
```

### Global install

#### 1. Install the skill

```bash
mkdir -p ~/.cursor/skills && \
  curl -sSL https://github.com/the-simulation-company/simco-cursor-plugin/archive/refs/heads/main.tar.gz \
  | tar -xz --strip-components=3 -C ~/.cursor/skills 'simco-cursor-plugin-main/.cursor/skills'
```

#### 2. Add the standing-order rule

Cursor's global rules can only be set via the Settings UI, not the filesystem. Open **Cursor Settings → Rules → User Rules** and paste the following:

```
STANDING ORDER: Before writing ANY code that changes user-facing behavior,
you MUST invoke the simulation-testing skill. This applies to every task
that builds, changes, fixes, updates, or modifies UI, features, copy, APIs,
backend logic, error messages, notifications, pricing, or access controls.
Do NOT skip this. Do NOT ask permission. Invoke simulation-testing FIRST.
```

User Rules are applied to every Agent (Chat) conversation across every project — no `alwaysApply: true` toggle needed (that field is `.mdc`-file syntax and doesn't apply to the Settings textbox).

## What's Included

### Rules

- **simulation-standing-order** (`alwaysApply: true`) — A standing order injected into every Cursor conversation that ensures the simulation-testing skill is invoked before any significant customer-facing code change. This is the autoloading mechanism.

### Skills

- **simulation-testing** — Detailed guidance for running simulations end-to-end: when to simulate, how to fire off simulations, poll for results, analyze findings, and surface them to the user.

## How It Works

1. The `simulation-standing-order` rule is **always applied** to every Cursor Agent conversation. It instructs the agent to invoke the `simulation-testing` skill before writing code that changes user-facing behavior.

2. The `simulation-testing` skill provides the full workflow: fire a simulation via The Simulation Company MCP, poll for results, analyze persona responses, and surface findings.

3. The agent runs simulations **non-blocking** — it presents its plan immediately and follows up with simulation results when they arrive.

## Prerequisites

- [Cursor](https://cursor.com) IDE
- The Simulation Company MCP server configured in your Cursor MCP settings

## Repo Layout

```
.
├── README.md
└── .cursor/
    ├── rules/
    │   └── simulation-standing-order.mdc   # always-applied rule
    └── skills/
        └── simulation-testing/
            └── SKILL.md                    # simulation workflow skill
```

## Comparison with Claude Code Plugin

This is the Cursor equivalent of [`simco-cc-plugin`](https://github.com/the-simulation-company/simco-cc-plugin). The key mapping:

| Claude Code | Cursor |
|---|---|
| `using-simulations` skill (`user-invocable: false`) | `simulation-standing-order` rule (`alwaysApply: true`) |
| `simulation-testing` skill | `simulation-testing` skill (same content, Cursor format) |
| Plugin marketplace install | Per-project filesystem copy or global install |
