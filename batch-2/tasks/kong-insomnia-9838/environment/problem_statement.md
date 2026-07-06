## Description

When importing a curl command into Insomnia, the resulting request does not automatically include an identifying application header. This means imported requests don't self-identify as coming from Insomnia unless the user manually adds a header, which is inconsistent with how Insomnia normally sends requests (which include a header showing the app name and version).

## Expected Behavior

- When a curl command is imported, the resulting request should automatically include an identifying header showing the application name and version.
- If the original curl command already includes its own custom identification header, it should be preserved as-is and not overridden — including when the header name is provided in different capitalizations.
- There should be an application-level setting to opt out of this automatic injection for users who do not want the application version included in their requests.
- Even when the automatic injection is disabled, any explicitly set identification header from the original curl command should still appear in the imported request.

## Why This Matters

Automatically injecting the application's identifying header into imported curl requests ensures consistency between how Insomnia normally sends requests and how imported requests behave. It helps servers identify request sources without requiring manual header additions from the user. The opt-out setting provides flexibility for users with privacy concerns about exposing the application version.
