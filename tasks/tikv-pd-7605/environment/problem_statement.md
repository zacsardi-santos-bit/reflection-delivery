## Description

The pd-ctl command-line tool needs a properly managed, globally accessible client for communicating with the PD server over HTTP. Currently, commands that retrieve cluster information use a raw HTTP client with no typed interface — there's no structured, lifecycle-managed client instance that can be initialized with server addresses and shared across commands.

This causes two problems: first, there is no typed API for querying cluster metadata and cluster status, so the cluster subcommands rely on raw HTTP calls that return untyped strings. Second, tests that exercise these commands have no way to set up a proper client connection before a command runs and no way to guarantee cleanup afterward.

## Expected Behavior

- The PD HTTP client interface should expose typed methods for retrieving cluster information and cluster status (with a corresponding cluster state data type that includes initialization status, replication status, and bootstrap time).
- A globally accessible, exported client variable should exist in the command package, allowing any command and test code to use a consistently initialized client.
- A setup function should be available to initialize this client with a list of PD server addresses, replacing any existing client and properly closing it first.
- The cluster subcommands should use this typed client to retrieve cluster data and output it as formatted JSON.
- The test helper that executes pd-ctl commands should initialize the client before running each command and ensure it is properly closed once the command completes.

## Why This Matters

Without this infrastructure, the cluster commands return raw unstructured output, resource leaks may occur because connections are never closed, and integration tests for cluster and ping commands fail. This change also establishes a pattern for migrating other pd-ctl commands from raw HTTP calls to the typed client interface.
