## Description

When a marimo notebook includes inline package dependency declarations (following the self-contained script standard for Python), those declarations are silently dropped when the notebook is exported to standalone script format. This is a significant issue for "sandboxed" notebooks, which depend on those inline declarations to manage their runtime environment automatically.

## Expected Behavior

- When exporting a sandboxed marimo notebook (one that carries inline dependency metadata) to script format, the exported output should include the full inline dependency block, exactly as it appeared in the original notebook.
- The dependency preservation should also work when the notebook has gone through a format conversion round-trip (for example, converted from Python format to Markdown format, and then exported back to script format). In both cases, the inline dependency information must be present in the final exported script.

## Current Behavior

Exporting a sandboxed notebook to script format drops all inline dependency metadata. The resulting script is incomplete — it cannot be run as a self-contained script by tools that rely on the inline declarations to install dependencies.

## Why This Matters

Users who share or deploy exported scripts from sandboxed notebooks expect those scripts to remain self-contained. Losing the dependency metadata means the exported script is no longer runnable without manual intervention to add back the dependency information. This undermines the value of the sandboxed notebook workflow.
