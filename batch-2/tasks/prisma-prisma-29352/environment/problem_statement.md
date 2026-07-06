## Description

We need a new CLI command that lets developers link their local project to a hosted Prisma Postgres database. Right now there is no built-in way to connect a local project to a cloud-managed database and automatically configure the local environment — developers have to manually copy connection strings, update their environment files, and figure out what to do next. This is error-prone and slows down onboarding.

## Expected Behavior

- Running the command with an API key and a database identifier should contact the management API, retrieve the connection string, and write it to the local environment file automatically.
- The command should support both a non-interactive mode (credentials and database ID provided as flags) and an interactive mode (browser-based login followed by guided selection of a project and then a database from the available options).
- When the environment variable for the API key is already set and a database is specified, the command should use that key without requiring the flag.
- When only one database is available under a selected project, the command should select it automatically without prompting.
- If the project already has a connection configured, the command should detect this and skip the API call, informing the user that the project is already linked and how to force a re-link.
- After successfully linking, the command should provide context-aware next steps — suggesting migration and client generation if a schema with data models already exists, or prompting the user to define their data model if not.
- The command should fail gracefully with clear error messages if no projects or no ready databases exist, or if credentials are invalid.
- Sensitive information (connection strings, API keys) must be redacted from error messages.
- If a session has expired (invalid refresh token), the command should automatically trigger a browser re-authentication and retry.

## Why This Matters

Linking a local development project to a cloud-hosted database should be a one-command operation. This removes manual steps, reduces setup friction, and ensures credentials are not accidentally committed.
