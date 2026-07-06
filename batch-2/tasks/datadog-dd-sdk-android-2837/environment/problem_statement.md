## Description

When the RUM SDK uploads batches of monitoring data to the Datadog backend, each request URL includes a tags query parameter that carries metadata used for routing and filtering events. Currently, essential context attributes — service name, application version, SDK version, environment, and build variant — are embedded inside each individual event payload rather than being attached at the request level. This means the backend cannot reliably filter RUM events by these standard dimensions using the transport-layer tag mechanism.

Additionally, these same context attributes are attached redundantly to every single event payload, bloating event data unnecessarily.

## Expected Behavior

- Every RUM upload request should always include a tags query parameter in its URL containing the service name, application version, SDK version, and environment.
- If a build variant is configured, it should also appear as a tag. If the variant is empty, it should be omitted.
- The tags parameter should always be present (not conditionally omitted when no retry tags exist).
- Retry-related information (retry count, last failure status) should continue to be appended after the context tags when applicable.
- Individual RUM event payloads should no longer carry a redundant tags field, since these attributes are now present at the request level.

## Why This Matters

Moving context tags from individual event payloads to the request URL ensures consistent, reliable metadata on every upload — even when events are processed or filtered before reaching storage. It also simplifies the event data model by removing a redundant field that duplicated information already available from the SDK context.
