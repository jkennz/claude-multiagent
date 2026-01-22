---
description: Run multiagent planning with specialist subagents through a 7-phase pipeline
allowed-tools: Read, Grep, Glob, Task, WebSearch, WebFetch
---

# Multiagent Planning Command

Execute comprehensive software engineering planning using the multiagent planner skill.

## Usage

```
/multiagent-planner:plan <task description>
```

## Parameters

- `$ARGUMENTS` - The task to plan (feature, bugfix, refactor, test, review)

## Examples

```
/multiagent-planner:plan fix the authentication timeout bug
/multiagent-planner:plan add user avatar upload feature
/multiagent-planner:plan refactor the billing module to reduce duplication
/multiagent-planner:plan write tests for the auth middleware
/multiagent-planner:plan review changes in the payment module
```

## Process

This command invokes the multiagent-planner skill which orchestrates specialist subagents through a 7-phase invariant pipeline:

1. **Frame** - Restate goal, constraints, acceptance criteria
2. **Context Pack** - Explorer maps codebase (always runs)
3. **Specialist Passes** - Route to relevant agents based on task type
4. **Synthesize Plan** - Combine outputs into canonical format
5. **Quality Gates** - Define verification steps
6. **Execute** - Optional implementation phase
7. **Review Pass** - Reviewer provides final audit (always runs)

## Task

Plan the following task using the multiagent-planner skill:

$ARGUMENTS
