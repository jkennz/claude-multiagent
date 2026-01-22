# Multiagent Planner

A Claude Code plugin that orchestrates specialist AI subagents through a structured 7-phase planning pipeline for software engineering tasks.

## What It Does

Instead of a single AI tackling your entire task, this plugin coordinates **8 specialized agents**, each with a focused role:

| Agent | Role | When Used |
|-------|------|-----------|
| **Explorer** | Maps codebase entry points and conventions | Always (Phase 2) |
| **Bug Finder** | Diagnoses root causes from errors/logs | Bug reports, failing tests |
| **Test Planner** | Designs verification strategy and test cases | Any code change |
| **Refactorer** | Plans safe incremental changes with rollback | Restructuring, cleanup |
| **Architect** | Analyzes cross-module impacts | Large or core changes |
| **Searcher** | Researches external frameworks/APIs | Unknown library behavior |
| **Advisory Panel** | Provides 3 perspectives (Ship-it/Clean/Risk) | High-stakes decisions |
| **Reviewer** | Final audit with risk assessment | Always (Phase 7) |

## Quick Start

### Installation

```bash
# Add the marketplace
/plugin marketplace add jkennz/claude-multiagent

# Install the plugin
/plugin install planner@claude-multiagent
```

Or for local development:
```bash
claude --plugin-dir /path/to/claude-multiagent
```

### Basic Usage

```bash
# Fix a bug
/planner:plan fix the authentication timeout bug

# Add a feature
/planner:plan add user avatar upload feature

# Refactor code
/planner:plan refactor the billing module to reduce duplication

# Write tests
/planner:plan write tests for the auth middleware

# Review changes
/planner:plan review changes in the payment module
```

## The 7-Phase Pipeline

Every task flows through the same invariant pipeline:

```
1. Frame          Restate goal, constraints, acceptance criteria
       │
       ▼
2. Context Pack   Explorer maps codebase (ALWAYS runs)
       │
       ▼
3. Specialists    Route to relevant agents based on task type
       │
       ▼
4. Synthesize     Combine outputs into canonical plan format
       │
       ▼
5. Quality Gates  Define verification steps by risk level
       │
       ▼
6. Execute        Optional: implement in small, verifiable steps
       │
       ▼
7. Review         Reviewer provides final audit (ALWAYS runs)
```

### Routing Examples

The orchestrator automatically routes to the right specialists:

- **Bug fix**: Explorer → Bug Finder → Test Planner → Reviewer
- **Feature**: Explorer → (Architect if cross-module) → Test Planner → Reviewer
- **Refactor**: Explorer → Refactorer → Test Planner → Reviewer
- **Tests only**: Explorer → Test Planner → Reviewer

## Key Design Principles

**Hub-and-Spoke Architecture**
- Main thread orchestrates; doesn't do deep analysis
- Subagents do focused work and return structured summaries
- No nested agents—orchestrator chains them sequentially

**Output Contracts**
- Every agent follows a strict output format
- Hard caps prevent context bloat (e.g., "Top 5 risks", "~40 lines max")
- Results reference paths/symbols, not paste entire files

**Progressive Disclosure**
- SKILL.md stays lean (<150 lines)
- Detailed specs live in reference files
- Agents load only what they need

## Directory Structure

```
claude-multiagent-planner/
├── .claude-plugin/
│   └── plugin.json              # Plugin manifest
├── commands/
│   └── plan.md                  # /planner:plan command
├── skills/
│   └── multiagent-planner/
│       ├── SKILL.md             # Main orchestrator instructions
│       └── references/
│           ├── routing-policy.md    # When to invoke which agent
│           ├── output-contracts.md  # Agent output formats
│           ├── quality-gates.md     # Verification by risk level
│           ├── plan-template.md     # Canonical plan structure
│           └── advisory-panel.md    # Three-perspective guidelines
├── agents/
│   ├── explorer.md              # Context pack builder
│   ├── bug-finder.md            # Root cause analyst
│   ├── test-planner.md          # Verification designer
│   ├── refactorer.md            # Incremental change planner
│   ├── reviewer.md              # Final auditor
│   ├── architect.md             # System implications analyst
│   ├── searcher.md              # External research specialist
│   └── advisory-panel.md        # Three perspectives in one
├── hooks/
│   └── hooks.json               # Completion verification hooks
└── README.md
```

## Optional: MCP Integration

The plugin can leverage MCP servers for enhanced capabilities:

### Perplexity MCP
For authoritative external research (used by Searcher agent):
```bash
# Set environment variable
export PERPLEXITY_API_KEY=your_key_here

# Configure in ~/.claude/settings.json or project settings
```

### Codex CLI MCP
For code review at plan completion:
```bash
# Install codex-cli MCP server
# The plugin will use it automatically when available
```

## Customization

### Adding a New Agent

Create a file in `agents/` with this structure:

```yaml
---
name: my-agent
description: When to invoke this agent
tools: Read, Grep, Glob
model: sonnet
color: blue
---

# My Agent

System prompt and instructions here...

## Output Contract

Define the exact format this agent must return.
```

### Modifying Routing

Edit `skills/multiagent-planner/references/routing-policy.md` to change when agents are invoked.

### Changing Output Formats

Edit `skills/multiagent-planner/references/output-contracts.md` to modify agent output templates.

## Troubleshooting

**Agents not being invoked**
- Verify agent files exist in `agents/` directory
- Check that the routing policy matches your task type
- Run `/agents` to see loaded agents

**Output too verbose or context overflow**
- Agents have strict line limits in their output contracts
- Check that agents reference file paths, not paste full contents

**MCP servers not working**
- Verify environment variables are set correctly
- Check `/mcp` command for server status

## How It Compares

| Approach | Pros | Cons |
|----------|------|------|
| Single Claude | Simple, low overhead | May miss edge cases, no specialized focus |
| **This Plugin** | Specialized analysis, structured output, consistent quality | More tokens, sequential phases |
| Manual multi-prompt | Flexible | Tedious, inconsistent, easy to forget steps |

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test with `claude --plugin-dir .`
5. Submit a pull request

## License

MIT
