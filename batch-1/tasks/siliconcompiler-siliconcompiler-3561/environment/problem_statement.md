## Description

SiliconCompiler currently manages tool configuration through the general chip schema, but lacks a dedicated class-based interface for working with tools at runtime. Functionality like locating an executable, checking its version, setting up environment variables, and building command lines is either scattered or not easily reusable by tool implementations.

We need a unified, object-oriented abstraction for tools that encapsulates all of this behavior in one place. The class should be able to bind itself to a running chip context (validating that a flow, step, and index are configured), and then provide consistent methods for:

- Discovering the executable path on the system
- Running the tool to check its version and comparing against configured requirements (with support for standard version specifier syntax, compound specifiers, and multiple acceptable version ranges)
- Assembling environment variables (global, tool-level license servers, and task-specific overrides, plus optional PATH management)
- Constructing the full command list for execution

The class should be part of the public API and exported from the main package. Runtime state (logger, schema references, step/index/task context) should be cleanly separated from configuration state, and copying the object should reset runtime state while preserving configuration.

Additionally, several tool-level configuration parameters (executable name, path, version, format, vendor, etc.) that were previously scoped at the job level should be promoted to global scope, since these settings belong to the tool itself rather than any specific job run. The schema version should be bumped accordingly.

## Expected Behavior

- A new tool class is available from the main package
- Binding it to a chip context raises clear errors if step, index, or flow are not configured
- Executable discovery, version checking with specifier support, environment variable assembly, and command construction all work through the class interface
- Deep-copying the tool object clears runtime state
- Tool-level configuration parameters have global scope in the schema

## Why This Matters

This consolidation reduces duplication, makes tool implementations more consistent, and provides a clear public API surface for writing and testing tool integrations.
