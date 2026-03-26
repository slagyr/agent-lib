---
name: plan-with-features
description: Plan work with Gherkin feature specification using gherclj. Use when the user says "/plan-with-features" or asks to plan with acceptance tests.
user-invocable: true
---

# Plan with Features

Extends the [/plan](plan.md) workflow with Gherkin feature specification using [gherclj](https://github.com/slagyr/gherclj).

Follow all instructions from `/plan`. This command adds a **feature-first** workflow: before creating beads, specify the desired behavior as Gherkin feature files. gherclj parses `.feature` files into EDN IR and generates executable test specs, so the features you write here become the acceptance tests.

## Feature-First Workflow

Replace steps 5-7 from `/plan` with:

5. **Draft features** - Present proposed `.feature` scenarios as text in the conversation. Do NOT write files yet.
6. **Refine** - Iterate based on feedback until the user approves
7. **Write & commit** - Write the `.feature` file with `@wip` tag, commit and push
8. **Create beads** - Create implementation beads that reference the feature file

## Writing Good Features

- **Read first** - Study existing `.feature` files, step definitions, and source code to match the project's style
- **Show real data** - Use actual values in doc-strings (EDN, generated code), not abstract assertions like "should have 3 items"
- **One responsibility per feature file** - Each feature covers a distinct concern (parsing, pipeline, CLI, etc.)
- **Behavior through examples** - Specify what happens with concrete inputs and outputs, not how it's implemented
- **Implementation details stay out** - Library choices, internal architecture, etc. belong in bead descriptions, not scenarios
- **`@wip` tag** - Mark the entire feature with `@wip` so unimplemented scenarios generate as pending

## Scenario Style

Prefer this:
```gherkin
  Scenario: Parse a minimal feature
    Given a feature file containing:
      """
      Feature: Login
        Scenario: Success
          Given a valid user
      """
    When the feature is parsed
    Then the IR should be:
      """
      {:feature "Login"
       :scenarios [{:scenario "Success"
                    :steps [{:type :given :text "a valid user"}]}]}
      """
```

Over this:
```gherkin
  Scenario: Parse a minimal feature
    Given a feature file with one scenario and one step
    When the feature is parsed
    Then the IR should have 1 scenario
    And the scenario should have 1 step
```

## Reminder

You are READ-ONLY on code files. Feature files and beads are your output. If you accidentally modify source files, immediately revert with `git checkout`.
