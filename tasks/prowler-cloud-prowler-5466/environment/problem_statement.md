## Description

Prowler does not currently support auditing AWS Step Functions state machines. This means security and compliance teams have no way to use Prowler to check whether their Step Functions workflows have logging configured, leaving a gap in their audit coverage for this service.

## Expected Behavior

- Prowler should be able to enumerate all Step Functions state machines across regions and retrieve their details, including logging configuration, tracing configuration, encryption configuration, and tags.
- A dedicated check should evaluate whether each state machine has logging enabled.
- State machines without logging enabled should be reported as a failing finding with a clear message indicating logging is absent.
- State machines with logging properly enabled should be reported as passing.
- The service integration should handle permission errors and missing resources gracefully without crashing.

## Why This Matters

Without logging enabled on Step Functions state machines, operational data may be lost, making it harder to troubleshoot issues and meet audit requirements. Providing visibility into which state machines lack logging helps teams remediate the gap and maintain compliance.
