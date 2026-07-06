## Description

The bulk secret upload commands currently only accept JSON-formatted files. Many developers maintain their secrets in dotenv-style files (lines of key-value pairs), such as .dev.vars or .env files. To bulk-upload secrets from these files today, they must first manually convert the file to JSON format — this is tedious and error-prone.

We should add support for accepting environment variable files directly, alongside the existing JSON support, for all three bulk upload contexts: regular Workers, Pages projects, and versioned Workers.

## Expected Behavior

- Passing an env-format file (.env, .dev.vars, etc.) with key-value pair entries to any of the bulk secret upload commands should successfully upload each key-value pair as a secret.
- The positional argument description in the CLI help text should be updated to mention both supported formats.
- The success output message should no longer say "JSON file" since the input may no longer be JSON.
- Error messages when no input is provided should be updated to be accurate and no longer reference JSON specifically.
- For the versioned Workers bulk upload command, invalid files that cannot be parsed as either format should produce a descriptive error that includes the filename and the specific parse failure details.

## Why This Matters

Developers commonly store secrets in dotenv-style files during development (e.g., .dev.vars for local Wrangler development). Being able to upload these files directly avoids unnecessary format conversions and keeps the workflow simple.
