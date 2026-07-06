## Description

When evaluating Codex agents that use skill files, two related problems make it difficult to diagnose why skills are not loading correctly.

First, when a skill read fails — for example because the shell environment is missing standard tools — the failed attempt is currently grouped together with successful skill reads in the evaluation result metadata. This means the skill calls reported in the result can include skills that were never actually loaded, making assertions on skill usage unreliable and hiding the root cause of failures.

Second, when custom environment variables are provided to override the Codex shell environment (for example, to set a custom home directory), the shell's PATH is stripped out entirely. This causes basic shell commands to fail with "command not found" errors, which in turn triggers the false-skill-call problem above.

## Expected Behavior

- Confirmed skill reads (where the read command completed successfully) should be reported as confirmed skill calls in the evaluation result.
- Failed skill read attempts should be tracked separately, so developers can tell when Codex tried to use a skill but couldn't load it.
- When custom environment variables are provided, a minimal set of essential shell variables — including the shell PATH — should still be passed to the agent's environment so that common shell commands remain available.
- Span trace attributes for skill usage should only be attached to command executions that completed successfully. Failed commands should still record their exit code and status, but should not be tagged as confirmed skill reads.

## Why This Matters

Skill-based evaluations are hard to debug when failed reads look identical to successful ones. Developers need accurate signal about which skills were confirmed versus merely attempted, and they need the agent's shell environment to be functional even when overriding specific variables.
