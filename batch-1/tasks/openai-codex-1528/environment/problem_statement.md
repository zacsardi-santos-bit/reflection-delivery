## Description

The Codex CLI tool needs a way to apply code changes produced by a remote coding agent task directly to a developer's local git repository. Currently, when an agent task completes and produces a diff output, there is no built-in command to retrieve and integrate that diff into the local working tree.

## Expected Behavior

- A new library crate should expose functionality to apply a task's diff output to the current local git repository using a 3-way merge strategy.
- When the diff applies cleanly (no conflicts with local state), the operation should succeed and the new or modified files should appear in the working tree with the correct content.
- When the incoming diff conflicts with locally committed content, the operation should fail and leave standard merge conflict markers in the affected files, so the developer can manually resolve the conflicts.
- The task response data type must support deserializing from a JSON structure that contains an optional current turn with a list of output items. Items of a PR type carry the actual diff text; other item types should be silently ignored.
- If the task response contains no current turn or no PR-type output item, the operation should return an error rather than silently doing nothing.

## Why This Matters

Developers using a remote coding agent need a seamless way to bring the agent's work into their local repository. Without this, they would have to manually extract and apply diffs. Proper conflict handling ensures that diverged local state is surfaced immediately rather than silently lost.
