## Description

When a developer introduces a syntax error in a source file while working with the hot module replacement (HMR) feature, the development server currently panics or exits instead of gracefully handling the error. This leaves the developer with no feedback in the browser and forces them to restart the server.

## Expected Behavior

- When a syntax error is introduced in a source file, a clear, identifiable error message should appear in the browser console indicating which file failed to parse. The message should include a recognizable prefix so developers immediately know it came from the HMR system.
- When the developer fixes the syntax error and saves the file, the HMR system should automatically recover and apply the updated module — the browser should reflect the corrected code without requiring a full server restart.

## Why This Matters

This is a critical developer-experience issue: a common workflow of "edit → error → fix" should work smoothly. Right now, introducing any syntax error during development kills the dev server process, which is especially problematic on Windows. Developers expect the server to stay running and provide actionable feedback in the browser when there are compile errors.

## Steps to Reproduce

1. Start the development server with HMR enabled
2. Introduce a syntax error in a source file (e.g., incomplete variable declaration)
3. Observe the dev server panics or exits, and no error appears in the browser console
4. Even if the server stays up, fixing the error does not trigger a successful HMR update
