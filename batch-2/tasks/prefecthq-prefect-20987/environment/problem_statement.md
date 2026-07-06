## Description

The Prefect CLI commands for viewing configuration settings and listing profiles currently only produce human-readable text output. This makes it very hard to use these commands in scripts, automation, or tooling that needs to parse the output reliably. There is no structured output option available.

## Expected Behavior

- The command to view current configuration settings should support a flag to output results as structured data (machine-readable format).
- When structured output is requested, each setting should include its name, current value, and the source it came from (e.g., environment variable, active profile). Secrets should be obfuscated.
- When structured output is requested alongside the option to hide sources, the source field should be omitted from the output.
- The command to list profiles should also support a flag to output results as structured data, where each profile entry indicates its name and whether it is currently active.
- Both commands should accept both a long-form and a short-form version of the output flag.
- If an unsupported output format is requested, both commands should fail with a clear error message.

## Why This Matters

Operators running automated deployments or writing shell scripts around Prefect often need to inspect settings or profile lists programmatically. Without a machine-readable output option, they are forced to parse fragile human-readable text. Adding structured output support makes these commands much more useful in CI/CD pipelines and scripting contexts.
