## Description

The containers listing command needs to be updated to use a newer API endpoint and provide significantly better output. Currently, running the list command in a non-interactive terminal outputs raw data that is difficult to read at a glance, and it doesn't show meaningful status information about each container's health. Users want to be able to quickly scan a table of their containers and see whether each one is healthy, degraded, or still provisioning — without having to parse a blob of data.

## Expected Behavior

- The list command should call the updated API endpoint for fetching container applications.
- In non-interactive mode, the command should render a clean, formatted table showing each container's ID, name, derived state, number of live instances, and last modified time.
- The state of each container should be derived from underlying health counters: a container with failing instances is "degraded", one with starting or scheduling instances is "provisioning", one with active healthy instances is "active", and an idle container with no running instances is "ready".
- A JSON output flag should be available for machine-readable output, producing objects with consistent fields.
- A per-page option should be available; passing 0 or a negative value should be rejected immediately with a clear error message.
- When no containers exist, the output should say so clearly rather than showing an empty structure.
- Client-side errors (bad request) and server-side errors should produce distinct, descriptive error messages.

## Why This Matters

Operators managing containerized workloads from the CLI need quick visibility into container health. The current raw dump makes it hard to act on the information. A structured table view with human-friendly state labels dramatically reduces cognitive load during operational tasks.
