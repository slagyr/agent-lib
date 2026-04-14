---
name: work
description: Pick up the next ready bead and work on it. Use when the user says "/work" or asks to start working on the next task.
user-invocable: true
---

# Work on Next Bead

Pick up the next ready bead and work on it.

## Steps

1. Run `bd ready` to find issues with no blockers
2. If no issues are ready, inform the user and stop
3. Select the highest priority issue (lowest P number) that is NOT in_progress
4. If the only ready issues are in_progress, select the highest priority issue and ask the user if they'd like to proceed. If they decline, stop
5. Show the issue details to the user with `bd show <id>`
6. Run `bd update <id> --status=in_progress` to claim it
7. Implement the issue, following any applicable project skills and conventions

## When Complete

1. Ensure all unit tests/specs pass
2. If the bead references approved feature scenarios, ensure those scenarios pass and are not pending
3. If the bead references approved feature scenarios, do not close the bead while those scenarios remain pending
4. If the bead references approved feature scenarios, do not change approved feature direction without review; if feature text and implementation diverge, stop and raise it
5. Check boot files (`AGENTS.md`, `CLAUDE.md`, etc.), existing beads, or `bd` config for the project's status flow. Do not skip this step. Use whatever status the project defines as the next step after `in_progress`. If no conventions are documented, default to `bd close <id>`.
6. Sync beads state using the installed bd version (`bd sync` if available; otherwise use the equivalent `bd dolt` commands)
7. Commit code changes with a descriptive message

## Arguments

$ARGUMENTS - Optional: specific issue ID to work on instead of picking from ready queue
