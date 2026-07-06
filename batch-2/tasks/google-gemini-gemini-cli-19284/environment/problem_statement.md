## Description

The CLI currently has no way to automate web browser tasks. Users who need to navigate websites, fill out forms, click buttons, extract information from web pages, or perform any browser-based automation must do so manually. A new experimental browser subagent is needed to handle these tasks programmatically.

## Expected Behavior

- A browser subagent should be available that can connect to Chrome and interact with web pages using the accessibility tree.
- The subagent should support multiple session modes: a persistent mode that preserves cookies and history between sessions, an isolated mode that uses a clean temporary profile, and an existing mode that attaches to an already-running browser.
- When a visual model is configured, the agent should be able to analyze screenshots to identify elements by visual properties (color, position, layout) that are not present in the accessibility tree.
- Connection errors should produce clear, actionable messages that guide the user to resolve the issue — for example, explaining which steps to take if the browser is already running or if the connection times out.
- Configuration should be readable from the settings file under a dedicated browser section with sensible defaults (persistent session mode, non-headless, no custom profile path, no visual model).

## Why This Matters

Browser automation is a common need for developers and power users who want to automate repetitive web tasks, perform automated testing, or extract data from websites. Without a built-in browser agent, users must rely on external scripts or manual work. This feature brings browser automation capabilities directly into the CLI workflow.
