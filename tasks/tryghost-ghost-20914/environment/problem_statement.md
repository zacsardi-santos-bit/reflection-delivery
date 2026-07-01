## Description

The email analytics system currently has a single method for retrieving the latest email events, fetching all event types together in one batch. This is problematic because "open" events occur far more frequently than delivery confirmations, failures, unsubscribes, and complaints. Bundling them together means the analytics pipeline cannot process different event types at different rates or on different schedules.

We need to split the single event-fetching method into two separate methods: one dedicated to open events, and another for all other event types (delivered, failed, unsubscribed, and complained). Each should be schedulable independently so the system can tune how often each type is polled.

In addition, there is no way for external consumers to inspect the current running state of each analytics job. A status-inspection method is needed that returns the job name and running state for all four analytics jobs.

## Expected Behavior

- Open events and non-open events must be fetched by separate, independently callable methods
- Each fetch method must properly filter events passed to the mail provider
- If the computed end timestamp for a fetch is before the begin timestamp, the fetch must be skipped
- A status method must return running state for all four job types with their correct job names
- A scheduled fetch must support cancellation and handle empty result sets gracefully
- A method for fetching "missing" events (events that were not captured in the last run) must be available
- The queries module must expose a consistently named method for retrieving the last event timestamp
- The mail provider must respect an explicit list of event types when supplied as an option

## Why This Matters

Separating event type fetching allows the Ghost email analytics pipeline to poll high-frequency events (like opens) more aggressively or less aggressively than low-frequency events (like bounces), improving both performance and accuracy of email analytics reporting.
