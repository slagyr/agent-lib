---
name: hardening
description: >-
  Project quality bars for agent work: coverage (and later crap, mutation,
  duplication). Load when implementing beans, running /harden, or enforcing
  .hardening.edn. General-purpose — thresholds and enabled steps come from
  the project file, not this skill.
---

# Hardening (project quality bars)

Read and follow the full procedure in:

**[commands/harden.md](../commands/harden.md)**

(That file is the source of truth: available steps, **defaults**, config
merge rules, coverage procedure, process-test skips, and bean tags.)

## Quick contract

1. Find **`.hardening.edn`** at the **implementation repo root**.
2. If missing, use skill **defaults** (see harden command).
3. Run only the steps listed in `:steps` (default `[:coverage]`).
4. Thresholds are **project-local** — never hardcode product numbers into
   toolbox or orchestration skills; change `.hardening.edn` instead.

## Defaults (summary)

| | |
| --- | --- |
| Default steps | `[:coverage]` |
| Coverage min line % | `90` |
| Default excludes | `#{:migrations :ui-islands}` |
| Stub steps (opt-in only) | `:crap`, `:mutation`, `:duplication` |

## Orchestration

Hail-driven harden loads `hail-bean-harden` then this procedure. The pipeline
only runs harden when the band **data** includes `harden-band` (optional).
