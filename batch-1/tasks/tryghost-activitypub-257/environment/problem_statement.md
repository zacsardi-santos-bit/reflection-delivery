## Description

The current post publishing flow is implemented inline inside the webhook handler, without any abstraction layer or clear separation of concerns. This makes it very difficult to test the publishing logic in isolation and hard to reason about each step of the process.

We need a proper publishing pipeline abstraction that:
- Defines clear interfaces for each component (resolving actors, building URIs, storing objects, managing the outbox, sending activities)
- Provides concrete implementations of those interfaces backed by the federation library
- Exposes a publishing service that composes these components to handle the full lifecycle of publishing a post to the federation

## Expected Behavior

- When a post is published, the system should create a preview object, a full article object, and a distribution activity, storing each one in the object store in that order
- The distribution activity should be added to the outbox
- The distribution activity should be sent to the author's followers via the federation
- If the author's federated identity cannot be resolved, the operation should fail with a clear error indicating which handle could not be resolved
- Storing an object that has no identifier should fail with a clear error
- Adding an activity to the outbox that has no identifier should fail with a clear error

## Why This Matters

Separating the publishing logic into composable, interface-driven components makes each part independently testable and opens the door for alternative implementations. The webhook handler can then delegate to this new pipeline rather than orchestrating federation details directly.
