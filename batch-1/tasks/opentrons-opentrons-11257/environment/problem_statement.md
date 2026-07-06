## Description

The Opentrons desktop app currently uses a hacky approach to let users change the path to the Python interpreter: a hidden file input element is embedded in the settings page, and clicking the "Add override path" button programmatically triggers a click on that hidden element. The browser then shows a file-selection dialog, and the app computes the directory from the selected file's path. This approach is fragile, browser-dependent, and requires a workaround to set non-standard directory-selection attributes that React doesn't support natively.

Additionally, the actions for managing the Python interpreter path (opening the current directory in the OS file explorer, or triggering a path change) currently live in the general configuration module, even though they are semantically related to protocol analysis. This creates unnecessary coupling between unrelated concerns.

## Expected Behavior

- Python interpreter path management actions (opening the current directory, and requesting a path change) should be moved to a dedicated protocol-analysis module rather than living in the general config module.
- When the user clicks "Add override path", the app should dispatch a shell-handled action that causes the app-shell to open a native OS directory picker dialog, receive the chosen path directly, and update the config. No hidden file input should be involved.
- When the user clicks the link to open the current Python interpreter directory in the file explorer, the action should come from the protocol-analysis module and the shell should handle it by opening the folder natively.

## Why This Matters

Using proper OS-native dialogs for directory selection is more reliable and maintainable than the hidden file input workaround. Moving Python interpreter management out of the generic config module into its own module improves separation of concerns and makes the codebase easier to reason about.
