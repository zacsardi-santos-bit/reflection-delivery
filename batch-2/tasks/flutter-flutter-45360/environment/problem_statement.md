## Description

When the Flutter tool crashes unexpectedly, developers currently see a minimal error message with little actionable information. There are no links to check for similar known issues, no guidance to the bug-reporting documentation, and no easy way to file a new bug report. We should improve this crash experience so developers can quickly find related issues and submit well-formatted reports.

Additionally, there is a bug where the tool crashes trying to read a project's Android compatibility extension setting when the relevant manifest section is entirely absent — it should safely return a default value of false instead of failing.

## Expected Behavior

- When a crash occurs, the tool should display a URL for searching similar existing issues on GitHub
- A link to the Flutter bug-reporting documentation guide should be shown
- A pre-filled GitHub issue template URL should be generated and shown so users can quickly file a new bug report
- Internal background messages about crash-data transmission should not appear in standard output — they should only appear in verbose/diagnostic output
- Reading the Android compatibility extension setting from a project manifest must safely return a default value of false when the relevant manifest section is absent, rather than crashing

## Why This Matters

Developers encountering a Flutter tool crash often don't know whether the problem is already known or how to properly report it. Providing contextual GitHub links at the time of a crash dramatically reduces friction and leads to better, more actionable bug reports.
