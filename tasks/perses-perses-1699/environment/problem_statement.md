## Description

The CLI tool for dashboard-as-code workflows is missing a built-in command to compile dashboard definition files into deployable YAML or JSON output. Currently, users must rely on an external tool and manually pipe its output to the deploy command — there is no integrated way to build these files directly from the CLI.

Additionally, two existing commands — apply and lint — have inconsistencies in how they handle input flags:

- The lint command only accepts a file as input and does not support a directory flag, even though a directory-based workflow makes sense (and apply already supports it).
- Both commands produce unclear or misleading error messages when neither a file nor directory flag is provided. The error messages do not clearly communicate that exactly one of the two flags is required.

## Expected Behavior

- A new build subcommand should be added that builds a given dashboard definition file (or all files in a directory) and saves the result to a dedicated output folder. The output format should default to YAML but optionally support JSON. An alternative mode should allow printing directly to standard output instead of writing to files.
- The apply and lint commands should both accept either a file or a directory as input, treating the two flags as mutually exclusive but requiring exactly one to be provided.
- When neither flag is provided, both commands should display a consistent, descriptive error message indicating that exactly one of the two flags is required.
- When both flags are provided simultaneously, the commands should clearly indicate that the flags cannot be used together.

## Why This Matters

Having a unified build command allows developers to evaluate their dashboard definitions and produce ready-to-deploy files without context-switching to an external tool. Consistent flag validation across CLI commands also improves usability and reduces confusion.
