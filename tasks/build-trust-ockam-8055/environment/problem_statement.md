## Description

The API for configuring telemetry and tracing exports when running commands in the foreground currently requires callers to pass a boolean "quiet mode" flag. This flag controls whether the exporter suppresses output, but this is an internal implementation detail — callers shouldn't need to know about it or make this decision explicitly.

## Expected Behavior

- Setting up a foreground tracing/logging export configuration should not require any arguments from the caller
- The export configuration should determine its own quiet/verbose behavior internally, based on its own logic and environment
- All call sites that currently pass a boolean to this method should be updated to use the new no-argument form

## Why This Matters

Exposing this internal flag in the public API creates unnecessary coupling between callers and the export configuration internals. Removing it simplifies the API, reduces cognitive overhead for anyone configuring tracing, and ensures that the configuration logic is encapsulated in one place rather than scattered across callers.
