# Agent Lib

A library of reusable skills, commands, and other agent components for AI coding agents.

## Structure

```
agent-lib/
  skills/       # Skills teach agents how to behave in specific contexts
  commands/     # Commands define reusable agent actions
```

## Available Skills

| Skill | Description |
|-------|-------------|
| [clojure](skills/clojure/SKILL.md) | Clojure coding conventions. Naming, formatting, function design, state management, threading, cross-platform patterns. |
| [c3kit](skills/c3kit/SKILL.md) | Guide to the c3kit ecosystem (apron, bucket, wire, scaffold). Schemas, database API, web layer, app lifecycle. |
| [tdd](skills/tdd/SKILL.md) | Test-Driven Development for Clojure. Red-Green-Refactor, the Three Laws, Arrange-Act-Assert with Speclj. |

For [toolbox](https://github.com/slagyr/toolbox) managed projects, add to your boot file:

```markdown
- [tdd](https://raw.githubusercontent.com/slagyr/agent-lib/main/skills/tdd/SKILL.md)
```

## Available Commands

| Command | Description |
|---------|-------------|
| [plan](commands/plan.md) | Planning mode using the beads issue tracker. Gathers context, discusses requirements, and creates beads. |
| [todo](commands/todo.md) | Convert TODO comments in the code into beads. |
| [work](commands/work.md) | Pick up the next ready bead and work on it. |

For [toolbox](https://github.com/slagyr/toolbox) managed projects, add to your boot file:

```markdown
- [plan](https://raw.githubusercontent.com/slagyr/agent-lib/main/commands/plan.md)
- [todo](https://raw.githubusercontent.com/slagyr/agent-lib/main/commands/todo.md)
- [work](https://raw.githubusercontent.com/slagyr/agent-lib/main/commands/work.md)
```
