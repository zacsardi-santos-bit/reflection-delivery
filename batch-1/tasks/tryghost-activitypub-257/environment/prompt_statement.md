I'm working on refactoring the post publishing flow in our ActivityPub-based application. Right now, the logic for converting a blog post into federated activities and sending them out is all tangled up inside the webhook handler, which makes it nearly impossible to test any of it in isolation.

I'd like to introduce a clean publishing pipeline with well-defined interfaces for each concern: resolving federated actor identities by handle, building URIs for ActivityPub objects, storing objects durably, tracking activities in an outbox, and sending activities to followers. Each interface should have a concrete implementation that delegates to the underlying federation library.

The main publishing service should accept these components through its constructor and, given a post (with an id, title, content, excerpt, feature image URL, publication date, URL, and author handle), should: resolve the author's actor, create a preview object and a full article object, wrap them in a distribution activity, store all three objects, add the activity to the outbox, and then send it to the author's followers.

If the actor cannot be resolved, the service should raise a clear error. If any object being stored is missing its identifier, that should also raise a descriptive error. The same applies when adding an activity without an identifier to the outbox.

All of these components and interfaces should be accessible from a single entry point in the activitypub module.
