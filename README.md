# Agent Lib

A library of reusable skills, commands, and other agent components for AI coding agents.

Components are managed by [toolbox](https://github.com/slagyr/toolbox).

## Structure

```
agent-lib/
  skills/       # Skills teach agents how to behave in specific contexts
  commands/     # Commands define reusable agent actions
```

## Usage

Add component sections to your project's boot file (`AGENTS.md`, `CLAUDE.md`, etc.) and reference components by URL:

```markdown
## Skills

This project uses [toolbox](https://raw.githubusercontent.com/slagyr/toolbox/main/SKILL.md)
to manage components. If `.toolbox/` doesn't exist, fetch the toolbox SKILL.md
from the URL above and follow its instructions.

- [tdd](https://raw.githubusercontent.com/slagyr/agent-lib/main/skills/tdd/SKILL.md)

## Commands

- [test](https://raw.githubusercontent.com/slagyr/agent-lib/main/commands/test.md)
```

See the [toolbox README](https://github.com/slagyr/toolbox) for full setup instructions.

## Available Skills

| Skill | Description |
|-------|-------------|
| [tdd](skills/tdd/SKILL.md) | Test-Driven Development for Clojure. Red-Green-Refactor, the Three Laws, Arrange-Act-Assert with Speclj. |

## Available Commands

| Command | Description |
|---------|-------------|
| [plan](commands/plan.md) | Planning mode using the beads issue tracker. Gathers context, discusses requirements, and creates beads. |
| [todo](commands/todo.md) | Convert TODO comments in the code into beads. |
| [work](commands/work.md) | Pick up the next ready bead and work on it. |
