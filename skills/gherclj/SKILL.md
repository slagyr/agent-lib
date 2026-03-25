---
name: gherclj
description: Use this skill when implementing gherclj feature steps, working on beads that reference .feature files, or writing defgiven/defwhen/defthen definitions. Ensures step definitions follow gherclj conventions — especially that defthen steps assert results.
---

# gherclj Step Implementation

## When This Skill Applies

Use this skill whenever you are implementing step definitions for `.feature` files in a gherclj project. This includes any bead that references a feature file or involves writing `defgiven`, `defwhen`, or `defthen` steps.

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

Then steps MUST assert using `gherclj.core` assertion functions. A `defthen` that returns a value without asserting is a bug — it produces 0 assertions and silently passes.

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
3. **Run:** `bb features` (or `bb test` for everything)

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
5. **All pass** — `bb features` shows 0 failures

Do NOT close a bead if the feature file still has `@wip` or if scenarios are pending.

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
