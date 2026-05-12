# Customer Simulation Skills for Cursor

A Cursor plugin that distributes skills for running customer simulations and acting on the results via [The Simulation Company](https://thesimulationco.com) MCP.

## Install

### Step 1: Install the skill

```bash
cd your-project
gh skill install the-simulation-company/simco-cursor-plugin --agent cursor --scope project --allow-hidden-dirs
```

This installs the `simulation-testing` skill into your project's `.cursor/skills/` directory.

### Step 2: Install the rule

```bash
mkdir -p .cursor/rules
curl -sSL -o .cursor/rules/simulation-standing-order.mdc \
  https://raw.githubusercontent.com/the-simulation-company/simco-cursor-plugin/main/.cursor/rules/simulation-standing-order.mdc
```

This installs the `simulation-standing-order` rule that automatically triggers the skill for customer-facing changes.

### Alternative: Manual Copy

```bash
git clone https://github.com/the-simulation-company/simco-cursor-plugin.git
cp -r simco-cursor-plugin/.cursor/ /path/to/your/project/.cursor/
```

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
- [GitHub CLI](https://cli.github.com/) (for `gh skill install`)
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
| Plugin marketplace install | `gh skill install` + `curl` for rule |
