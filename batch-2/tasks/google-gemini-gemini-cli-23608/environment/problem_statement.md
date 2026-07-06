## Description

The CLI tool supports a "Plan Mode" where agents are restricted to only creating or modifying plan files, rather than making direct source code changes. However, agents currently lack awareness of this mode in two important ways: their capability descriptions do not change to reflect what they're actually allowed to do in Plan Mode, and the instructions injected into sub-agent system prompts don't mention the file-write restrictions at all.

Additionally, when a user opens a plan confirmation dialog for a plan file that doesn't exist on disk, the error shown is a confusing storage initialization message rather than a clear "file not found" indication.

## Expected Behavior

- When the tool is in Plan Mode, a general-purpose agent's description should reflect the planning and investigation focus (e.g., referencing "large-scale investigation and batch planning") rather than describing code-modification tasks like batch refactoring and error fixing.
- When the tool is in Plan Mode, the system instructions sent to sub-agents should include a clearly labeled constraints section explaining that write capabilities are restricted to plan files only within the designated plans directory.
- When a plan confirmation dialog references a plan file that does not exist, the error displayed should clearly state that the file was not found (including the path), not show a generic storage error.

## Why This Matters

Users relying on Plan Mode need agents to behave predictably and communicate their restrictions clearly — both in their capability descriptions and in the instructions they receive. Unclear or missing constraint communication leads to agents attempting forbidden operations. Similarly, a confusing error message when a plan file is missing makes it harder to diagnose and recover from the problem.
