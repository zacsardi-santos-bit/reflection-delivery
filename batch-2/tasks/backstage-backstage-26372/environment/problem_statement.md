## Description

Backstage's backend framework currently lacks a built-in, standardized service for recording audit events. Plugins that need to track security-relevant operations — such as data access, configuration changes, or entity mutations — have no structured way to capture what happened, whether it succeeded or failed, and what contextual metadata was associated with it.

## Expected Behavior

- A new auditing service should allow plugin developers to create a structured audit event at the start of an operation.
- The event should have a clear lifecycle: initiated, succeeded, or failed.
- Metadata provided when creating the event should be carried forward and merged with any additional metadata provided at completion (success or failure).
- When an operation fails, the error details should be captured as part of the audit record.
- The catalog backend plugin's router creation should accept and integrate the auditing service.

## Why This Matters

Without a dedicated auditing service, teams have no consistent way to produce audit trails for backend operations. This change makes it possible for plugin developers to track the start and outcome of security-relevant operations in a structured, uniform manner that can be extended across the platform.
