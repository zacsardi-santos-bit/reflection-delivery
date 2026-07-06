## Description

Currently, setting up remote task logging in Airflow requires users to write and configure a custom logging module — a brittle, error-prone process that puts unnecessary burden on operators. There is no standard way for providers to advertise that they can handle remote log storage for a given URL scheme. As a result, each user has to manually wire up the connection between a log storage URL and the provider that handles it.

We need a mechanism where providers can declare which URL scheme they support for remote task logging, and the providers manager discovers and registers these handlers automatically. The logging system can then select the right handler based on the configured log storage URL without requiring a custom logging module.

## Expected Behavior

- Providers can declare remote logging handler metadata (a class path and a URL scheme) in their provider data.
- The providers manager discovers these declarations and registers handlers by scheme, making them addressable at runtime.
- If two providers register the same scheme, the first one registered wins and the duplicate is silently ignored.
- If a provider's declared class path cannot be imported, that entry is skipped without error.
- A shared logging factory resolves the active remote log handler using a clear priority order:
  1. A user-defined logging module's configuration wins, if one has been set.
  2. Automatic provider-based selection by URL scheme (when remote logging is enabled).
  3. A legacy fallback for backward compatibility.
- The logging system caches the resolved handler and connection ID to avoid redundant lookups.
- A clean API exposes the active remote log handler and default connection ID, with lazy loading and explicit cache control.
- The existing public API for loading the logging configuration is preserved but marked as deprecated with an appropriate warning.

## Why This Matters

This change makes remote logging setup self-service: users simply configure a storage URL, and the correct provider handler is activated automatically. It eliminates the need for custom logging modules in the common case, reduces configuration complexity, and gives provider authors a standard way to participate in Airflow's logging infrastructure.
