## Description

The marimo server currently does not include any workspace context in the page configuration it serves for notebooks. This means the language server integration has no way to know the project root directory or the full path of the notebook file being edited, which limits its ability to provide accurate code intelligence (completion, navigation, diagnostics).

We need the server to compute and embed workspace information — the project root URI and the notebook document URI — in the page configuration sent to every served notebook page.

## Expected Behavior

- When a specific notebook file is served, the page configuration should include the project root directory (determined by scanning ancestor directories for a project configuration file) and the full path of that notebook as the document URI.
- When a directory is used (e.g., browsing or creating a new notebook), the project root should be found the same way, and a default notebook filename should be used for the document URI.
- When there is no discoverable project structure or no directory context, the server should fall back to using the current working directory as the project root.
- When the workspace cannot be determined or is not applicable (e.g., in export/static mode), the workspace field in the page configuration should explicitly indicate that no workspace is available.

## Why This Matters

Without this information, the language server cannot reliably locate the project root or understand which file is being edited, resulting in degraded code intelligence. Embedding the workspace details in the page configuration allows the language server integration to initialize with the correct project context right from page load.
