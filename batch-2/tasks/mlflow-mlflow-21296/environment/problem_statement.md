## Description

When inspecting DSPy model traces in the model trace explorer, the section delimiters that DSPy inserts into its outputs are currently shown as raw bracketed syntax (e.g. double-bracket markers surrounding a section name). This makes trace content hard to read because the delimiters look like technical noise rather than semantic structure.

## Expected Behavior

- Standalone section markers that appear on their own line should be rendered as formatted section headings, making it immediately clear where each named section begins.
- A special termination marker should be silently removed from the output since it carries no meaningful information for a human reader.
- Markers that appear embedded inside inline code should be left exactly as-is, preserving technical accuracy.
- Section names that use underscores as word separators should be displayed with spaces and proper capitalization.
- When a DSPy trace response contains a JSON object, it should be rendered as a properly formatted, syntax-highlighted code block rather than plain text, improving readability and clarity.

## Why This Matters

DSPy traces currently expose raw internal formatting to users, making them harder to read compared to other model types. Proper rendering would make traces easier to understand for anyone inspecting DSPy-powered model behavior in the explorer.
