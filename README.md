# Agent Skills

A library of reusable skills for AI coding agents, managed by [toolbox](https://github.com/slagyr/toolbox).

## Using These Skills

Add a `## Skills` section to your project's `AGENTS.md` and reference skills by URL:

```markdown
## Skills

This project uses [toolbox](https://raw.githubusercontent.com/slagyr/toolbox/main/SKILL.md)
to manage skills. If `.skills/` doesn't exist, fetch the toolbox SKILL.md
from the URL above and follow its instructions.

- [tdd](https://raw.githubusercontent.com/slagyr/agent-skills/main/tdd/SKILL.md)
```

See the [toolbox README](https://github.com/slagyr/toolbox) for full setup instructions.

## Available Skills

| Skill | Description |
|-------|-------------|
| [tdd](tdd/SKILL.md) | Test-Driven Development for Clojure. Red-Green-Refactor, the Three Laws, Arrange-Act-Assert with Speclj. |
