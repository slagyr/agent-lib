---
name: plan-with-features
description: Plan work with Gherkin feature specification using gherclj. Use when the user says "/plan-with-features" or asks to plan with acceptance tests.
user-invocable: true
---

# Plan with Features

Extends the [/plan](plan.md) workflow with Gherkin feature specification using [gherclj](https://github.com/slagyr/gherclj).

Follow all instructions from `/plan`. This command adds a **feature-first** workflow: before creating beads, specify the desired behavior as Gherkin feature files.

Load the [`gherkin`](https://raw.githubusercontent.com/slagyr/agent-lib/main/skills/gherkin/SKILL.md) skill for writing good scenarios.

## Feature-First Workflow

Replace steps 5-7 from `/plan` with:

5. **Draft features** - Present proposed `.feature` scenarios as text in the conversation. Do NOT write files yet.
6. **Refine** - Iterate based on feedback until the user approves
7. **Write & commit** - Write the approved `.feature` file with `@wip` tag, commit and push
8. **Create beads** - Create implementation beads that reference the feature file

## @wip Convention

- New features start with `@wip` at the top of the file
- `@wip` scenarios are excluded from generation — they produce no test code
- Removing `@wip` is part of the implementation bead's definition of done
- A bead is NOT complete if its feature file still has `@wip`

## Modifying Existing Features

- Add new scenarios with `@wip` tag on the individual scenario, not the whole file
- When changing expected behavior, update the scenario's expected output — the implementation bead should make it pass
- When removing a scenario, remove it from the feature file and verify tests still pass
- Commit feature file changes before creating the implementation bead — the failing/pending test is the spec

## Beads and Features

- Every implementation bead should reference the feature file it implements
- One bead per feature file is typical, but large features may be split across beads
- Infrastructure beads (parser changes, framework updates) may block feature implementation beads
- Feature files are the spec; bead descriptions cover implementation approach

## Read First

Before drafting features, study existing `.feature` files and step definitions to match the project's style. Understand what steps already exist before proposing new ones.

## Reminder

You are READ-ONLY on code files. Feature files and beads are your output. If you accidentally modify source files, immediately revert with `git checkout`.
