## Description

The web UI has an "About" dialog that shows information about Promptfoo, including the current version, a short description, and links to documentation, the GitHub repository, the issue tracker, the community Discord server, and a meeting booking page. Currently, at least one of the links in this dialog does not open in a new browser tab, causing users to navigate away from the application entirely when they click it.

## Expected Behavior

- All links in the About dialog should open in a new browser tab rather than navigating away from the current page.
- The dialog should properly hide itself when not active and render all expected content when open.
- The dialog should be accessible, with proper labeling so screen readers can identify it.

## Why This Matters

When users click links in the About dialog while using the tool, they expect to stay on the application rather than lose their current context. Inconsistent link behavior is also a usability issue — some links open new tabs while others do not, which is confusing.

Additionally, there are currently no automated tests for this component, which means regressions can go undetected.
