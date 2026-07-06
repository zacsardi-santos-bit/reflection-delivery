## Description

We need a Go client package for integrating with the SAP Alert Notification Service (ANS). Currently, there is no dedicated package in the library for sending events to this notification service or verifying that the service connection is correctly configured. Pipeline steps that want to publish alerts or notifications have no standard, tested way to do so.

## Expected Behavior

- A client type should be able to send structured events to the notification service using an authenticated HTTP request. On success (HTTP 202), no error is returned. On failure, an informative error is returned.
- A connectivity check operation should verify that the notification service is reachable and properly configured, using an authenticated HTTP GET. On success (HTTP 200), no error is returned.
- The client should be configurable from a service key, which bundles the backend URL and OAuth credentials together.
- Service keys should be parseable from JSON strings; malformed input should produce a clear error.
- Events should be composable: it must be possible to merge a JSON-formatted event definition into an existing event object, combining tags and resource metadata from both sources.
- Events must be validatable — invalid category, severity, priority range, or negative timestamps should produce descriptive error messages.
- The client should automatically map standard log severity levels to the notification service's own severity and category vocabulary.

## Why This Matters

Pipeline steps need a reliable, well-tested way to publish structured alerts and notifications to the SAP Alert Notification Service. Without a shared client package, each step would need to implement its own HTTP handling and credential management, leading to inconsistency and duplicated logic.
