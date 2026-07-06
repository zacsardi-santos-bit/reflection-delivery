## Description

When marimo exports a notebook as a static HTML page (or as a WebAssembly-powered interactive page), the embedded runtime currently has no way to know that the notebook code it is about to run originated from a trusted, server-generated export. This means the runtime cannot automatically grant trust to notebook-authored resources such as virtual data files — users need to manually execute cells before those resources are accepted.

We need to embed a tamper-resistant, frozen trust marker directly in the exported HTML. This marker should identify the page as a genuine server export and carry the complete original notebook source code. The marker must be set up so that it cannot be overwritten by other scripts running on the page.

## Expected Behavior

- Every exported static notebook HTML page includes a JavaScript block that installs a frozen, read-only export context object in the page's global JavaScript scope
- The export context object marks the page as trusted and carries the full original notebook source code
- The export context block appears in the page head, after the existing static data block
- The same export context block is also injected into WebAssembly-powered notebook pages
- The embedded interactive runtime can read this context to determine trust without requiring manual user cell execution

## Why This Matters

Without this trust marker, exported notebooks viewed offline or embedded in external pages cannot automatically load notebook-authored data files and other virtual resources. The marker allows the runtime to correctly grant trust to all notebook resources from the moment the page loads, making shared and embedded notebooks work reliably out of the box.
