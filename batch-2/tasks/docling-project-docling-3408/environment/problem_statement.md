## Description

The CLI's help output contains two small but noticeable issues that make it less user-friendly.

1. **Spelling typo in a debug option's description**: One of the debug visualization options describes "layour clusters" instead of "layout clusters". This typo is visible to anyone who runs the help command.

2. **Confusing positional argument name**: The main positional argument (the input file paths) is internally named in a way that leaks directly into the help output as-is. This raw internal name is verbose and unhelpful to users; a shorter, cleaner name should be used instead.

3. **Unclear help text for the input format option**: The description for the input format selection option uses awkward phrasing. It should be updated to more clearly describe what the option does and what happens by default.

## Expected Behavior

- Running the tool with `--help` should display "layout clusters" (correctly spelled) for the debug visualization option.
- The positional argument for specifying input files should appear with a clean, user-friendly name in the help output — not as an internal Python identifier.
- The input format selection option should have a clear help message that begins with "Input formats to" and mentions "all supported" formats as the default.

## Why This Matters

Users frequently run `--help` to understand what options are available. Typos and exposed internal parameter names undermine trust in the tool and make it harder to understand. These are quick but impactful polish fixes to the CLI's self-documentation.
