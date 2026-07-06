## Description

The AI setup checklist item marks itself as "done" too eagerly. Currently it only checks whether the AI setup command was recorded in the event log — but that event is written regardless of whether the agent actually produced any story files in the current project. In a monorepo this means running the AI setup command in one package incorrectly flips the checklist to "done" for every other package in the repo.

Additionally, users who have telemetry disabled never see the correct opt-in status in the checklist because the opt-in flag was only being read from the telemetry event cache, which is a no-op for them.

## Expected Behavior

- The AI setup checklist item should only be marked complete when the AI setup command ran for the **specific project** being viewed **and** at least one AI-generated story actually exists in that project's story index.
- Running the AI setup command in a sibling monorepo package must not affect the checklist for the current package.
- The opt-in status should be determined from a per-project filesystem cache so it works correctly even when telemetry is disabled.
- If the AI setup ran but no AI-generated stories have been created yet, the checklist item should remain open.
- The checklist should still load immediately without waiting for these checks, and should handle cache read failures gracefully.

## Why This Matters

Without this fix, the checklist gives false confidence that AI-generated content is ready when none exists, and behaves incorrectly in monorepos and for users with telemetry disabled.
