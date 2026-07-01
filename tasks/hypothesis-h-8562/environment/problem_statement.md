# Refactor: Extract Annotation Sync Logic into a Dedicated Service

## Description

The search index service currently handles two distinct responsibilities: managing the Elasticsearch index for annotations, and synchronizing the index by comparing database state against what is stored in the search index. This mixing of concerns makes the code harder to understand and maintain.

The sync logic — which reads from a job queue, compares annotations in the database to those in Elasticsearch, and decides whether to re-index, skip, or clean up jobs — should live in its own focused service rather than being bundled into the search index service.

## Expected Behavior

- The annotation synchronization logic is extracted into its own dedicated service.
- The search index service is simplified and no longer accepts or manages the queue, database session, or batch indexer as constructor dependencies.
- The indexer task that triggers annotation synchronization delegates to the new dedicated sync service rather than the search index service.
- The new sync service is registered and available as a standard service (with factory support) like other services in the application.

## Why This Matters

Keeping two unrelated responsibilities in the same service violates the single-responsibility principle and makes both services harder to test in isolation. Separating the sync logic makes each service smaller, more focused, and independently testable. It also makes the dependency graph clearer — consumers that only need to trigger a sync no longer need to depend on the full search index service.
