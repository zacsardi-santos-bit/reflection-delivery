## Description

When an AI assistant connects to a running Storybook instance, it retrieves a list of available commands via the initial connection handshake. However, the server can also return workflow instructions during that same handshake — project-specific guidance such as "use existing stories as examples" or "run tests after writing stories." Currently, this guidance is silently discarded and never reaches the AI assistant.

Additionally, the help text that the AI assistant sees lacks clear section structure: there is no heading for the commands section, the header wording is slightly off, and there is no way to display workflow instructions even if they were available.

## Expected Behavior

- A new API should be available that, in addition to fetching the tool list, also returns any server-provided workflow instructions captured during the initialization handshake.
- Workflow instructions should be extracted and trimmed. If the value is absent, empty, whitespace-only, or not a string, it should be treated as if no instructions were provided.
- If the initialization handshake fails for any reason (bad response, protocol error, missing data), the system must still successfully return the tools list — server metadata failures should not block tool retrieval.
- When a failed handshake response has a body stream, the stream must be properly released/canceled.
- The help text output must include a clearly labeled "Storybook commands" section heading.
- When workflow instructions are available, they must be displayed in a separate "Storybook workflow instructions" section that appears before the commands list.
- The help text header must be updated to say "Storybook help from the Storybook running at..." instead of the current phrasing.

## Why This Matters

AI assistants that integrate with Storybook can provide much better guidance when they have access to project-specific workflow instructions alongside the list of available commands. Without this, each project's best practices are invisible to the AI, leading to suboptimal or inconsistent story generation. This change also improves help output readability with proper section headings.
