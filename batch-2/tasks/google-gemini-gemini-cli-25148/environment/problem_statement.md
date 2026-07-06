## Description

The skill inbox currently only surfaces newly extracted skills — entire new skill directories that users can review and install. However, the memory extraction process can also identify improvements to skills that are already installed in the user's global or workspace directories. Right now, there is no mechanism to propose, review, or apply those incremental updates. Users miss out on refinements that the system has detected.

## Expected Behavior

- When the memory extraction process identifies improvements to existing skills, it should produce update proposals as patch files alongside the new skill directories.
- The inbox dialog should display both new skills and skill updates together, with clear section labels separating the two categories when both are present.
- Selecting a skill should show a preview of its contents before offering install options.
- Selecting a skill update should show the proposed diff so users can review the exact changes before deciding to apply or discard them.
- Applying an update should be atomic — either all changes succeed, or nothing is written — so a failure never leaves a skill in a partially updated state.
- Workspace trust settings should be respected: updates targeting the current workspace's skills must be blocked when the workspace is not yet trusted.
- When extraction produces new skill updates, the system should notify the user that updates are waiting in the inbox for review.

## Why This Matters

Without this feature, the memory system can only suggest brand-new skills, not refine ones the user already relies on. Supporting incremental updates lets the system continuously improve existing skills based on patterns observed in real sessions, and gives users a safe, reviewable workflow for accepting or rejecting those improvements.
