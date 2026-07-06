## Description

The language server's tool interface passes document information to each tool as multiple separate parameters: a file location, optional text content, and (for formatting) a language identifier. These loose parameters must be threaded through every level of the call stack — from the backend down through the worker layer and into individual tool implementations. As the set of per-document metadata continues to grow, this approach becomes fragile and repetitive.

We should introduce a single unified document object that bundles the file location, language identifier, and optional text together. All methods in the tool interface and the worker that currently accept separate URI and content arguments should be updated to accept this document object instead.

## Expected Behavior

- A new public document type exists in the language server crate that groups a file's URI, its language identifier, and its optional text content into one value.
- The document type has a constructor that accepts all three fields.
- All tool interface methods for running diagnostics (on open, on change, on save) and formatting accept this document object as their sole document-related parameter.
- The worker methods that aggregate diagnostics and formatting across tools are updated consistently to use the same document object.
- All existing tool implementations are updated to use the new parameter shape.
- The file system helper used internally constructs and returns this document object rather than returning a raw tuple.

## Why This Matters

Bundling document metadata into a single object makes the API cleaner, reduces the risk of parameter mismatch bugs, and makes it straightforward to add new per-document fields in the future without updating every method signature again.
