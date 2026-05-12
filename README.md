# Customer Simulation Skills for Cursor

A Cursor plugin that distributes skills for running customer simulations and acting on the results via [The Simulation Company](https://thesimulationco.com) MCP.

## Install

### Option 1: GitHub CLI (Recommended)

Install with [GitHub CLI](https://cli.github.com/) using `gh skill install`:

```bash
cd your-project
gh skill install the-simulation-company/simco-cursor-plugin --agent cursor --scope project
```

This installs both the rules and skills into your project's `.cursor/` directory.

### Option 2: Manual Copy

```bash
# Clone this repo
git clone https://github.com/the-simulation-company/simco-cursor-plugin.git

# Copy into your project
cp -r simco-cursor-plugin/.cursor/ /path/to/your/project/.cursor/
```

### Option 3: Git Submodule

```bash
cd your-project
git submodule add https://github.com/the-simulation-company/simco-cursor-plugin.git .cursor-plugin
# Then symlink or copy the .cursor directory
ln -s .cursor-plugin/.cursor .cursor
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
| Plugin marketplace install | `gh skill install` or manual copy |
