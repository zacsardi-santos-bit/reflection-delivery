## Description

The Azure AI Search connector currently creates a search client for collections by reading private, internal attributes from the Azure SDK's search index client object to retrieve the endpoint and credential. This approach is fragile and breaks when those private attributes are unavailable — for example, when a user provides a pre-built client instead of letting the library create one automatically. Additionally, credential resolution logic is embedded inside the client creation function, making it harder to test and reuse in isolation.

## Expected Behavior

- The store and collection objects should explicitly expose the service endpoint and credential as proper, publicly accessible attributes.
- When a store is created by passing in a pre-built search index client, retrieving a collection from that store should still succeed — the system should be able to resolve the connection details independently rather than extracting them from private SDK attributes.
- A dedicated, publicly exported credential resolution helper should be available that cleanly selects the appropriate credential (key-based or token-based) and raises a clear error when no credential can be resolved.
- The credential helper should use the asynchronous token credential type rather than the synchronous one.

## Why This Matters

Developers who manage their own Azure SDK clients — for example, to customize retry policies or share a client across multiple components — are unable to use the store's collection creation workflow. Making endpoint and credential first-class attributes on the store and collection objects removes the dependency on SDK internals and enables these workflows to succeed reliably.
