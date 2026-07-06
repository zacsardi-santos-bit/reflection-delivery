Implement a text search capability for Prefect's events and logs, allowing users to search through content by keyword with a flexible query syntax. Ensure the search is case-insensitive and supports multilingual content. Integrate this feature into both in-memory and database-backed filtering systems.

*   Create a new module at `src/prefect/server/utilities/text_search_parser.py`:
    *   Define a `TextSearchQuery` dataclass with fields `include`, `exclude`, and `required`, all defaulting to empty lists.
    *   Implement `parse_text_search_query(query: str) -> TextSearchQuery` to parse a query string into a `TextSearchQuery` object.
        *   Parse space-separated terms as OR-logic `include` terms.
        *   Add terms prefixed with `-` or `!` to the `exclude` list.
        *   Add terms prefixed with `+` to the `required` list.
        *   Ignore solo `-`, `!`, or `+` characters.
        *   Preserve original case and handle Unicode characters.
        *   Parse quoted phrases as single terms, applying prefix rules.
        *   Handle backslashes inside quoted phrases for literal quotes and backslashes.
        *   Treat single quotes as ordinary characters.
        *   Ensure an unclosed quote consumes the rest of the input as a phrase.

*   Update `EventFilter` in `src/prefect/server/events/filters.py`:
    *   Add an optional `text: EventTextFilter | None` field.
    *   Implement `EventTextFilter` class:
        *   Define `query: str` to hold the raw query string.
        *   Implement `_build_searchable_text(event) -> str` to concatenate event type, resource label values, and payload content.
        *   Implement `includes(event) -> bool` to check if an event matches the text filter.

*   Update `LogFilter` in `src/prefect/server/schemas/filters.py`:
    *   Add an optional `text: LogFilterTextSearch | None` field.
    *   Implement `LogFilterTextSearch` class:
        *   Define `query: str` to hold the raw query string.
        *   Implement `includes(log) -> bool` to check if a log matches the text filter by searching `log.message` and `log.name`.

*   Ensure `query_events` and `read_logs` apply the text filter when the `text` field is set, producing consistent results with in-memory implementations.

*   Ensure the text search:
    *   Supports exclusion with `-` and `!` prefixes.
    *   Allows quoted phrase exclusion.
    *   Is composable with other filters, such as log level filters.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.