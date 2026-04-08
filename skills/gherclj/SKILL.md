---
name: gherclj
description: Use this skill when implementing gherclj feature steps, working on beads that reference .feature files, or writing defgiven/defwhen/defthen definitions. Ensures step definitions follow gherclj conventions — especially that defthen steps assert results.
---

# gherclj Step Implementation

## When This Skill Applies

Use this skill whenever you are implementing step definitions for `.feature` files in a gherclj project. This includes any bead that references a feature file or involves writing `defgiven`, `defwhen`, or `defthen` steps.

## Feature Contract Integrity

Approved `.feature` files are behavioral contracts.

- Do NOT semantically weaken, reinterpret, or rewrite approved scenarios without user approval.
- Clarifying wording changes are fine only when they preserve the approved behavior exactly.
- If implementation and approved feature text diverge, stop and raise the mismatch instead of changing the scenario to fit partial implementation.

## Step Definitions Must Exercise Real Behavior

Step definitions should test real product behavior through real code paths where feasible.

- Prefer calling production entry points, public APIs, application services, or other real seams the product already exposes.
- If a scenario needs a seam, prefer a real public seam or an explicitly approved test hook.
- Do not move product logic into step definitions just because the implementation is missing.

## Forbidden Acceptance-Test Shortcuts

- Do NOT inject unimplemented product behavior directly in step definitions just to make scenarios pass.
- Do NOT add acceptance-test-only shims that simulate promised behavior the product does not actually implement.
- Passing feature scenarios must reflect actual implemented behavior, not test-only shortcuts.
- A bead is not complete if the feature passes only because the steps fake missing behavior.

## Step Types and Their Responsibilities

### defgiven — Set up preconditions

Given steps mutate state to establish preconditions. No assertions needed.

```clojure
(defgiven create-user "a user \"{name}\""
  [name]
  (g/assoc! :user {:name name}))
```

### defwhen — Perform actions

When steps perform the action under test. No assertions needed.

```clojure
(defwhen user-logs-in "the user logs in"
  []
  (let [user (g/get :user)]
    (g/assoc! :response (authenticate user))))
```

### defthen — Assert results

Then steps MUST assert. A `defthen` that returns a value without asserting is a bug — it produces 0 assertions and silently passes.

If your project uses a single framework, you can use its native assertions directly (e.g., speclj's `should=`). Use `g/should=` when your steps need to be framework-agnostic — for example, when the project runs tests under both speclj and clojure.test.

```clojure
;; WRONG — no assertion, silently passes
(defthen check-status "the response status should be {status:int}"
  [status]
  (g/get-in [:response :status]))

;; RIGHT — asserts the expected value
(defthen check-status "the response status should be {status:int}"
  [status]
  (g/should= status (g/get-in [:response :status])))
```

## Common Assertion Patterns

```clojure
;; Exact match
(g/should= expected (g/get :key))

;; Truthiness
(g/should (g/get :key))
(g/should-not (g/get :key))

;; Nil check
(g/should-be-nil (g/get :key))
(g/should-not-be-nil (g/get :key))

;; Collection
(g/should= expected-vec (g/get :items))
```

## Verification

After implementing steps, always run the feature specs and verify:

1. **Assertion count > 0** — If you see `0 assertions`, your `defthen` steps are not asserting
2. **No unexpected pending** — Pending scenarios mean step text isn't matching registered steps
3. **Real behavior is exercised** — Scenarios pass because the product implements the behavior, not because steps simulated it
4. **Run:** `bb features` (or `bb test` for everything)

```
# Good — assertions present
40 examples, 0 failures, 68 assertions, 5 pending

# Bad — no assertions means defthen steps aren't asserting
40 examples, 0 failures, 0 assertions, 5 pending
```

## Definition of Done

A feature implementation bead is NOT complete until:

1. **Step definition file exists** — `src/gherclj/features/steps/<feature>.clj`
2. **All scenarios run** — Remove the `@wip` tag from the feature file
3. **No pending scenarios** — Every scenario's step text matches a registered step
4. **Assertions > 0** — Every `defthen` step asserts
5. **No fake behavior in steps** — Step definitions are not simulating missing product behavior
6. **All pass** — `bb features` shows 0 failures

Do NOT close a bead if the feature file still has `@wip`, if scenarios are pending, or if the feature only passes because the steps fake missing behavior.

## State Management

Steps use `gherclj.core` for state, aliased as `g`:

```clojure
(ns myapp.features.steps.auth
  (:require [gherclj.core :as g :refer [defgiven defwhen defthen]]))
```

- `g/assoc!`, `g/assoc-in!` — set state
- `g/get`, `g/get-in` — read state
- `g/update!`, `g/update-in!` — modify state
- `g/swap!` — arbitrary state transformation
- `g/dissoc!` — remove state
- `g/reset!` — called automatically before each scenario
