---
name: todo
description: Convert TODO comments in the codebase into beads. Use when the user says "/todo" or asks to create beads from TODOs.
user-invocable: true
---

# Create Beads from TODOs

Convert TODO comments in the code into beads.

1. Find all TODO comments in the codebase
2. Skip any TODOs that already have a bead id
3. Consolidate similar TODOs into a single bead when appropriate
4. Create a bead for each TODO (or group)
5. Update each TODO comment to include the bead id, e.g.: `TODO (bead-id) - original text...`
