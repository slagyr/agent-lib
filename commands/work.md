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
5. Only then run `bd close <id>` to mark the issue complete
6. Run `bd sync` to sync changes
7. Commit code changes with a descriptive message

## Arguments

$ARGUMENTS - Optional: specific issue ID to work on instead of picking from ready queue
