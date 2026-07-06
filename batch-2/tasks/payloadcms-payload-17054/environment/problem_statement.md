## Description

The Payload MCP plugin exposes CMS operations to AI assistants, but currently only covers the core document lifecycle (create, read, update, delete). Several common and useful operations are missing, which forces AI tools to work around limitations or simply cannot perform them.

## Missing Capabilities

- **Count documents**: There is no way to ask "how many documents match this filter?" without fetching the full list and counting client-side.
- **Find distinct values**: There is no tool to retrieve all unique values for a given field across a collection.
- **Duplicate a document**: There is no way to copy an existing document and optionally override some fields on the copy.
- **Version management for collections**: When a collection has versioning enabled, AI assistants cannot count versions, list versions, retrieve a specific version by ID, or restore a prior version.
- **Version management for globals**: Similarly, when a global has versioning enabled, none of these version management operations are available.

## Expected Behavior

- A count tool should be available for collections and return the number of documents matching an optional filter.
- A tool should exist to list all distinct values for a specific field in a collection.
- A duplicate tool should be available for collections, creating a copy of a document with an optional set of field overrides.
- Document duplication should be disabled by default for authentication-related collections, and calling it on a disabled collection should produce a clear error.
- For collections and globals with versioning enabled, tools to count versions, list versions, retrieve a version by ID, and restore a version should all be available.

## Why This Matters

AI assistants using MCP to manage Payload content currently have a significant gap in what operations they can perform. Adding these tools brings the MCP integration up to feature parity with the underlying Payload API for common read and content-management tasks, and ensures version-aware workflows are fully supported.
