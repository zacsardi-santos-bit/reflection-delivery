## Description

The Prefect server events system needs a database-backed storage layer that can write, query, and count events stored in a relational database. Currently, there is no standardized module for persisting events to the database or retrieving them with filtering and pagination. This is blocking features that rely on event history, event analytics, and event-driven automation.

## Expected Behavior

- Events can be written to the database in bulk and retrieved later.
- Events can be queried with rich filters: by event name (exact match, prefix, exclusion), by primary resource (ID, ID prefix, labels, with wildcard support), by any resource (primary or related), by related resource (ID, role, label, wildcard), and by event ID.
- Query results are paginated, returning a page of events, a total count, and an opaque token to retrieve the next page.
- Results are ordered by occurrence time, defaulting to descending order; ascending order is also supported.
- An empty related-resource filter should not restrict results unexpectedly.
- Events can be counted grouped by time intervals, event type, or primary resource. Time-based counts backfill empty intervals with zero, anchored to a stable fixed reference point so that overlapping queries over the same data produce consistent bucket boundaries.
- Time interval counting validates that the interval is large enough (minimum 0.01) and that the resulting number of buckets is within a reasonable limit.
- A cross-database-compatible utility function is available for extracting JSON fields, working correctly with both PostgreSQL and SQLite backends (including a mode that wraps keys in quotes for dotted key names on SQLite).
- A boolean constant indicating which major version of the data validation library is installed is available in the appropriate internal module.

## Why This Matters

Without this storage layer, the events system is write-only and cannot support querying, dashboarding, automation triggers based on event history, or event analytics. The counting feature enables time-series visualizations and resource-based event breakdowns.
