## Description

Two issues need to be addressed in the autosuggestion plugin:

1. **Performance: allow disabling automatic widget re-binding** — Currently, autosuggest re-binds all widgets on every prompt rendering (every precmd). This is necessary to ensure compatibility with other plugins that wrap widgets, but it has a noticeable performance cost. Users who don't need this automatic re-binding should be able to opt out.

2. **Async mode: Ctrl-C does not properly interrupt** — When asynchronous suggestion fetching is enabled and a user starts typing (triggering a background suggestion fetch), pressing Ctrl-C should terminate the current prompt and begin a new one. However, the current async implementation (based on a pseudo-terminal) causes Ctrl-C to misbehave in this situation instead of cleanly starting a new prompt.

## Expected Behavior

- A configuration option should allow users to disable automatic widget re-binding. When this option is set, widget list changes do NOT take effect until the user manually triggers a rebind by calling the bind function explicitly.
- Once the user manually triggers the rebind, the updated widget bindings should work correctly going forward.
- In async mode, pressing Ctrl-C after starting to type (and triggering a suggestion fetch) should cleanly terminate the current prompt and show a new empty prompt on the next line.

## Why This Matters

The automatic re-binding on every precmd is a significant performance bottleneck for users with large widget lists or many plugins. Giving users control over when re-binding happens allows them to trade some convenience for a meaningful speed improvement. Additionally, fixing the Ctrl-C behavior in async mode makes the plugin more reliable in interactive use.
