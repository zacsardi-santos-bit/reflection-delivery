Refactor the existing search index service by extracting the annotation synchronization logic into a new dedicated service. Implement this new service to handle all queue-processing and comparison logic, simplifying the search index service and adhering to the single-responsibility principle.

*   Create a new `AnnotationSyncService` class in `h/services/annotation_sync.py`.
    *   Implement the constructor `__init__(self, batch_indexer, db, es, queue_service)`.
    *   Implement the method `sync(self, limit)` that:
        *   Fetches jobs from the queue service.
        *   Returns a dictionary of count strings.
        *   Returns an empty dictionary and performs no indexing when the queue is empty.
        *   Handles jobs with `force=True` by indexing the annotation and deleting the job from the queue.
        *   Deletes jobs from the queue without indexing when the annotation is not found or marked as deleted in the database.
        *   Indexes missing annotations in Elasticsearch without deleting the job.
        *   Deletes jobs from the queue without indexing when annotations are up-to-date in Elasticsearch.
        *   Re-indexes annotations with different timestamps or user IDs in Elasticsearch.
        *   Indexes each annotation only once when multiple jobs reference the same ID, counting/deleting jobs individually.

*   Provide a `Result` class or enum in `h/services/annotation_sync.py`.
    *   Ensure members produce exact string keys for sync outcomes using `.format(tag=...)` or equivalent.
    *   Include members such as `SYNCED_TOTAL`, `COMPLETED_TOTAL`, `SYNCED_TAG_TOTAL`, `COMPLETED_TAG_TOTAL`, `SYNCED_FORCED`, `COMPLETED_FORCED`, `SYNCED_MISSING`, `SYNCED_DIFFERENT`, `COMPLETED_UP_TO_DATE`, and `COMPLETED_DELETED`.

*   Implement a `factory(context, request)` function in `h/services/annotation_sync.py`.
    *   Create a `BatchIndexer` using `(request.db, request.es, request)`.
    *   Return an `AnnotationSyncService` using `(batch_indexer, request.db, request.es, queue_service)`.

*   Update the `SearchIndexService` in `h/services/search_index.py`.
    *   Remove `db`, `batch_indexer`, and `queue_service` from its constructor parameters.
    *   Update its factory function to no longer create a `BatchIndexer` or pass those parameters.

*   Modify the `sync_annotations` indexer task.
    *   Call `annotation_sync_service.sync()` instead of `sync()` on the search index service.

*   Ensure an `annotation_sync_service` fixture is available in the common test fixtures.
    *   Export it from `tests/common/fixtures/services.py`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.