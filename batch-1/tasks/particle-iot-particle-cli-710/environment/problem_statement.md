## Description

The CLI tool for managing cloud logic has inconsistent capitalization throughout its user-facing text. "Logic Functions" is a proper product feature name, but commands, error messages, help descriptions, and interactive prompts all refer to it in lowercase ("logic function", "logic functions"). This should be treated consistently as a proper noun — "Logic Function" / "Logic Functions" — everywhere the user sees it.

Additionally, when a Logic Function is being executed or deployed, the tool currently prints a plain status line to standard output before starting the operation. It would be better to show an animated busy indicator while the operation is in progress, so users clearly see that work is happening rather than the output looking like a static log line.

Finally, there is a mismatch between what the cloud API now returns when a Logic Function is deployed and what the client code expects. The API response wraps the function's metadata (ID and version) in a nested field, but the client tries to read those values from the root of the response — so after deploying, the function's ID and version are not correctly captured.

## Expected Behavior

- All user-facing text (help strings, error messages, prompts) consistently capitalizes "Logic Function" / "Logic Functions" as a proper noun.
- When executing a Logic Function, the tool shows a busy indicator while the operation is running instead of a plain output line.
- After deploying a Logic Function, the tool correctly reads the function's ID and version from the cloud API response.
- Error messages from listing, finding, executing, and deploying Logic Functions all use the correct capitalization.

## Why This Matters

Inconsistent capitalization makes the product feel less polished. The spinner improvement gives users a clearer signal that work is in progress. The API response fix ensures that deploying a Logic Function correctly captures its ID and version for follow-up operations.
