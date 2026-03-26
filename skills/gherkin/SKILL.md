---
name: gherkin
description: Use this skill when writing, reviewing, or planning Gherkin feature files (.feature). Covers scenario design, step writing, anti-patterns, and acceptance test strategy.
---

# Gherkin

## When This Skill Applies

Use this skill whenever you are writing, reviewing, or planning `.feature` files — regardless of the framework (gherclj, Cucumber, Behave, SpecFlow, etc.).

## Scenario Design

### One behavior per scenario

Each scenario tests one thing. If you need "and also check that..." it's a second scenario.

```gherkin
# GOOD — focused
Scenario: Admin can log in
  Given a user "alice" with role "admin"
  When the user logs in
  Then the response status should be 200

Scenario: Guest is rejected
  Given a user "bob" with role "guest"
  When the user logs in
  Then the response status should be 401
```

```gherkin
# BAD — testing two behaviors
Scenario: Login behavior
  Given a user "alice" with role "admin"
  When the user logs in
  Then the response status should be 200
  Given a user "bob" with role "guest"
  When the user logs in
  Then the response status should be 401
```

### Concrete examples over abstract assertions

Show real data. Don't describe structure.

```gherkin
# GOOD — concrete, verifiable
Then the IR should be:
  """
  {:feature "Login"
   :scenarios [{:scenario "Success"
                :steps [{:type :given :text "a valid user"}]}]}
  """
```

```gherkin
# BAD — vague, hides what matters
Then the IR should have 1 scenario
And the scenario should have 1 step
```

### Scenario names describe the outcome

Name what happens, not what you're testing.

```gherkin
# GOOD
Scenario: Expired token is rejected
Scenario: Empty cart shows helpful message

# BAD
Scenario: Test token validation
Scenario: Cart edge case
```

## Step Writing

### Given — establish context

Set up the world. No assertions, no actions.

```gherkin
Given a user "alice" with role "admin"
Given an empty cart
Given the system has 3 products
```

### When — perform the action

One action per scenario. If you have multiple When steps, you're testing multiple things.

```gherkin
When the user logs in
When the user adds "Widget" to the cart
```

### Then — assert the outcome

Verify observable results. Be specific.

```gherkin
Then the response status should be 200
Then the cart should contain 1 item
Then the error message should be "Invalid credentials"
```

### And/But — continue the previous type

```gherkin
Given a user "alice" with role "admin"
And a valid session token
When the user logs in
Then the response status should be 200
And the response should contain a session cookie
But the response should not contain the password
```

## When to Use What

### Background vs repeated Given steps

Use Background when every scenario in a feature shares the same setup.

```gherkin
# GOOD — shared setup in Background
Background:
  Given a registered user "alice"

Scenario: View profile
  When alice views her profile
  Then she sees her name

Scenario: Edit profile
  When alice edits her name to "Alice Smith"
  Then her profile shows "Alice Smith"
```

Don't use Background for setup that only some scenarios need.

### Scenario Outline vs individual scenarios

Use Scenario Outline when scenarios differ only in data, not behavior.

```gherkin
# GOOD — same behavior, different data
Scenario Outline: Role-based access
  Given a user with role "<role>"
  When the user accesses the admin panel
  Then the response should be <status>

  Examples:
    | role  | status |
    | admin | 200    |
    | guest | 403    |
    | user  | 403    |
```

Don't use Outline when the scenarios have different steps or different meaning.

### Tables vs doc-strings

Tables for structured, multi-column data:

```gherkin
Given the following users:
  | name  | role  | active |
  | alice | admin | true   |
  | bob   | guest | false  |
```

Doc-strings for blocks of text, code, or data formats:

```gherkin
Then the IR should be:
  """
  {:feature "Login"
   :scenarios [{:scenario "Success"
                :steps [{:type :given :text "a valid user"}]}]}
  """
```

### Tags

Use tags to categorize and filter scenarios:

- `@wip` — not yet implemented
- `@smoke` — critical path, run first
- `@slow` — takes time, skip in fast feedback loops

Tags can go on features (apply to all scenarios) or individual scenarios.

## Anti-Patterns

### Too many steps

If a scenario has more than 5-7 steps, it's doing too much. Split it or create higher-level steps.

### Implementation details in steps

```gherkin
# BAD — exposes implementation
Given a row is inserted into the users table with name "alice"
When a POST request is sent to /api/login with JSON body {"user": "alice"}

# GOOD — business language
Given a user "alice"
When alice logs in
```

### Asserting in Given steps

Given steps set up state. They should never verify anything.

### Incidental details

Only include details that matter to the scenario.

```gherkin
# BAD — irrelevant details
Given a user "alice" with email "alice@example.com" created on "2024-01-15" with role "admin"
When the user logs in
Then the response status should be 200

# GOOD — only what matters
Given a user "alice" with role "admin"
When the user logs in
Then the response status should be 200
```

## Feature File Organization

- One feature per file
- Name the file after the feature: `authentication.feature`, `checkout.feature`
- Group related features in a directory: `features/`
- Keep features close to the code they test
