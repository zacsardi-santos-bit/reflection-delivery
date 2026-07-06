## Description

The Pulumi CLI sends HTTP requests to the Pulumi Cloud backend, and currently the user-agent header only contains the CLI version and operating system. This makes it impossible to correlate backend API traffic to specific CLI subcommands or to determine whether an AI coding assistant (like a code agent or AI-powered IDE) was responsible for triggering the CLI.

As AI-assisted development becomes more common, it would be valuable to:
1. Know which CLI command was being run when an API request was made.
2. Know whether the CLI was invoked by a human or by an AI coding tool.

## Expected Behavior

- There should be a way to set the current CLI command on the HTTP client so it is automatically included in the user-agent string sent with every API request.
- There should be a way to set a detected AI agent name on the HTTP client so it is also included in the user-agent string.
- When a command or AI agent name is set, the user-agent should include them in a structured format within the existing parenthesized comment section.
- Special characters in the command name (parentheses, semicolons, spaces) should be sanitized to keep the user-agent header well-formed.
- If neither is set, the user-agent should remain in its existing format without any new fields.
- The function that detects which AI coding environment is active should be accessible from outside its current package so it can be called during CLI startup.

## Why This Matters

This enriches observability on both the client and server side, allowing the Pulumi team and users to understand usage patterns and attribute API calls to specific commands and tooling contexts. It also enables the CLI to automatically detect a growing set of AI coding environments and report them accurately.
