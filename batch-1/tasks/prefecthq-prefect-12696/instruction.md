Implement a database-backed storage layer for the Prefect server events system to support writing, querying, and counting events stored in a relational database. Ensure the system handles event filtering, pagination, and counting by various dimensions, while maintaining cross-database compatibility.

*   Implement the `count_events` function in `src/prefect/server/events/storage/database.py`:
    *   Accept parameters: `session`, `filter` (EventFilter), `countable` (Countable enum), `time_unit` (TimeUnit enum), and `time_interval` (float).
    *   Return a list of `EventCount` objects.
    *   Handle time-based counting with intervals anchored to `PIVOT_DATETIME`, backfilling empty intervals with zero counts.
    *   Raise a `ValueError` if `time_interval` is less than 0.01 or if the bucket count exceeds 1000, with specific error messages.
    *   Support counting by event name and primary resource, ordering results by total count descending.

*   Implement the `write_events` function in `src/prefect/server/events/storage/database.py`:
    *   Accept `session` and a list of `ReceivedEvent` objects.
    *   Write events to the database.

*   Implement the `query_events` function in `src/prefect/server/events/storage/database.py`:
    *   Accept `session`, `filter` (EventFilter), and optional `page_size`.
    *   Return a tuple of (list of `ReceivedEvent`, total count, optional page token).
    *   Default to descending order by occurred time; support ascending order.
    *   Ensure an empty related-resource filter does not restrict results.

*   Implement the `query_next_page` function in `src/prefect/server/events/storage/database.py`:
    *   Accept `session` and a `page_token`.
    *   Return the same tuple type as `query_events`.

*   Implement the `from_page_token` function in `src/prefect/server/events/storage/__init__.py`:
    *   Decode an opaque page token string.
    *   Raise `ValueError` for invalid tokens.

*   Ensure `EventNameFilter`, `EventResourceFilter`, `EventAnyResourceFilter`, and `EventRelatedFilter` support specified filtering criteria, including wildcard patterns.

*   Ensure queried events have timezone-aware datetime fields with string representations ending in '+00:00'.

*   Implement the `json_extract` function in `src/prefect/server/utilities/database.py`:
    *   Accept `column`, `key`, and optional `wrap_quotes`.
    *   Generate appropriate SQL for PostgreSQL and SQLite, handling `wrap_quotes` correctly.

*   Define the `HAS_PYDANTIC_V2` constant in `src/prefect/_internal/pydantic/__init__.py` to indicate the installed version of Pydantic.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.