---
name: harden
description: >-
  Harden recently verified bean work against project quality bars (coverage,
  and later crap/mutation/duplication). Use when the user says "/harden", when
  a hail delivers work to a harden band, or when implementing and checking
  project quality thresholds.
user-invocable: true
---

# Harden Bean Work

Enforce **project quality bars** after acceptance verification. You are not
re-checking Gherkin acceptance (that is verify). You are checking engineering
quality: coverage, and other steps as configured.

You are a **reviewer of quality**, not the implementer. Be thorough but fair.
Prefer failing with a clear delta over silent pass.

## Config file: `.hardening.edn`

Look for **`.hardening.edn`** at the **implementation repo root** (the product
code checkout named by the bean — not necessarily the beans repo). If the
beans repo *is* the implementation repo, use that root.

| Situation | Behavior |
| --- | --- |
| File **missing** | Use **skill defaults** below (steps + thresholds). |
| File present | Merge: file wins for keys it sets; defaults fill gaps. |
| `:steps` present (including `[]`) | Run **exactly** those steps (empty = pass, nothing to do). |
| `:steps` omitted | Use default step list. |

### File shape

```edn
;; Example project file — thresholds and which steps run are project-local.
{:steps [:coverage]
 :coverage
 {:min-line-pct 90
  :exclude #{:migrations :ui-islands}}}
```

Do **not** invent thresholds not present in defaults or the file. Do **not**
edit global skills to change a project's bar — change `.hardening.edn`.

## Available steps

| Step key | Default? | Status | Purpose |
| --- | --- | --- | --- |
| `:coverage` | **yes** | implemented | JVM unit line coverage (Cloverage / project suite) ≥ min % |
| `:crap` | no | **stub** | CRAP score / complexity-risk (e.g. crap4clj) under max |
| `:mutation` | no | **stub** | Mutation score (e.g. clj-mutate) ≥ min |
| `:duplication` | no | **stub** | Duplication / DRY budget (e.g. dry4clj) under max |

**Default step list** when `:steps` is omitted and no file (or file omits
`:steps`):

```edn
[:coverage]
```

**Stub steps:** if a project lists a stub step in `:steps`, **fail** the
harden with a clear note that the step is configured but not automated yet
(unless the bean is a process-test — see below). Do not pretend to pass.
When implementing a new step, document it in this table and remove the stub
label.

### Default thresholds (when not overridden)

```edn
{:coverage
 {:min-line-pct 90
  ;; Exclude from the product gate (see Coverage procedure).
  :exclude #{:migrations :ui-islands}}}
```

`:crap`, `:mutation`, and `:duplication` have **no defaults** until
implemented — projects that enable them must set thresholds in `.hardening.edn`.

## Process-test / no-op beans

If the bean body says **process test**, **no-op**, or **orchestration smoke**
(same suspension as work):

- **Skip all quality steps.**
- Append a short `## Harden` note: steps skipped for process test.
- Pass the harden gate (remove `unhardened` tag; leave status `completed`).

## The harden gate (bean tags)

Convention (parallel to verify's `unverified`):

| Stage | Status | Tag |
| --- | --- | --- |
| After work, awaiting verify | `in-progress` | `unverified` |
| After verify pass, **no** harden-band | `completed` | (none) |
| After verify pass, **with** harden-band | `completed` | `unhardened` |
| After harden pass | `completed` | (none) |
| Harden fail | `in-progress` | (none) + fail note |

- **Pass:** `beans update <id> --remove-tag=unhardened` (status stays
  `completed`). Commit + push beans.
- **Fail:** `beans update <id> --status=in-progress --remove-tag=unhardened`
  and append a `## Harden fail (...)` note. Hand back to the work band (when
  hail-driven).

## Steps (manual / hail-driven)

1. Pull latest in beans repo and implementation repo (`git pull --rebase`).
   Do not race pull with bean reads.
2. Sanity-check worktrees (`git status --porcelain`). Dirty trees: abort and
   report — do not auto-clean.
3. Load the bean (`beans show <id>` or queue `beans list --tag=unhardened`).
4. Resolve implementation repo root; load `.hardening.edn` or defaults.
5. If process-test / no-op → pass path above and stop.
6. Run each step in the resolved `:steps` list (order as listed).
7. All steps pass → remove `unhardened`, notify, done.
8. Any step fails → fail path, hand back with evidence (metric, threshold,
   namespaces or files under the bar).

## Coverage procedure (`:coverage`)

**Goal:** product code meets `:min-line-pct` line coverage (Cloverage-style
line metric: a line counts if any form on it ran).

**Scope (product gate):**

- Include application namespaces under the project's instrumented source
  (typically `src/`).
- **Exclude** when `:exclude` contains:
  - `:migrations` — schema migration ns under persistence migrations paths
  - `:ui-islands` — interactive UI islands (combobox, modal interaction
    paths, heavy `*.ui` CLJC islands). **Do not** demand coverage acrobatics
    or reader-conditionals for cosmetics.
- Prefer the project's documented filter if `.hardening.edn` adds more
  exclude keys later; unknown exclude keys: report and skip that filter.

**How to measure (Clojure / speclj house default):**

- Run the unit suite under Cloverage (project recipe if present; otherwise
  speclj unit lane excluding slow tags, instrumenting `src`).
- Report overall product-gate % and worst offenders under the bar.
- Full-suite coverage is acceptable; if the bean only touched a small set
  of namespaces, also report those — but the **gate** is the product filter
  unless `.hardening.edn` defines a narrower `:scope`.

**Pass:** product-gate line % ≥ `:min-line-pct`.  
**Fail:** below bar — list low namespaces and approximate miss mass.

**Guideline vs CI:** this is a **quality bar for harden / agents**, not
necessarily a hard CI fail, unless the project wires it into CI separately.

## Crap / mutation / duplication (stubs)

When implemented, each step will document:

- Tool invocation
- Metric and comparison
- Pass/fail rule

Until then, listing them under `:steps` fails harden (except process-test).

## Notifications (when hail-driven)

Use notification-comm from the delivery data block. Formats for hail-driven
runs are defined in `hail-bean-harden`. Manual `/harden` may report in chat
only.

## Workers (not only hardener)

Implementers **should** load this command (or the project `.hardening.edn`)
while building, so delivery already aims at the bar. Harden **enforces**;
workers **strive**. Never weaken acceptance scenarios to pass harden.
