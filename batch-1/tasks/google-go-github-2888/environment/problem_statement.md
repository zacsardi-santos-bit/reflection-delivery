## Description

The go-github library is missing support for Dependabot alert webhook events. GitHub sends these webhook events when Dependabot detects a new vulnerability in a repository's dependencies, or when an alert is dismissed, fixed, or otherwise updated. Without support for this event type, developers using the library cannot process these webhooks in a structured way.

Additionally, the existing alert data model is missing a field that represents when an alert was automatically dismissed, which means that information is silently dropped when consuming Dependabot alert data from the API.

## Expected Behavior

- A new structured event type should exist for Dependabot alert webhooks, carrying details about the action taken, the alert itself, the affected repository, organization, enterprise, sender, and installation context.
- The webhook message parser should recognize the Dependabot alert event type and return a properly typed value.
- The hook delivery system should also be able to map Dependabot alert payloads to the correct struct.
- The alert data model should include a field for the automatic dismissal timestamp.
- All new fields and types should support idiomatic nil-safe access patterns consistent with the rest of the library.

## Why This Matters

Developers who need to react to Dependabot alerts via GitHub webhooks (for example, to track or respond to security vulnerabilities in their repositories) currently have no first-class way to do so through this library. Supporting this event type closes that gap and keeps the library consistent with GitHub's webhook API surface.
