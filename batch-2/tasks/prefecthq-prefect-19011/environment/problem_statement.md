## Description

Prefect currently has no text search capability for events or logs. Users browsing events or log entries can only filter by structured fields (resource ID, time range, log level, etc.) but cannot perform a keyword or phrase search across the actual content — the event name, resource values, payload data, or log messages.

This is a significant gap when debugging incidents or searching for specific patterns. A user who remembers seeing "connection timeout" in a log message, or wants to find all events that mention "database" in their payload, has no way to retrieve those results quickly.

## Proposed Feature

Add a text search filter that works across both events and logs with a rich query syntax:

- **OR logic by default**: space-separated terms each act as independent match candidates — if any match, the item is returned
- **Exact phrase matching**: wrapping terms in double quotes finds only items containing that exact phrase
- **Exclusion**: prefixing a term (or quoted phrase) with `-` or `!` filters out any item containing that text
- **Required terms**: prefixing with `+` marks a term as mandatory (AND logic, for future use)
- **Case-insensitive**: searches work regardless of capitalization
- **Empty search**: an empty query returns all results, equivalent to no text filter
- **Multilingual support**: searches work correctly with international characters and Unicode content

For events, the search should cover the event type, resource label values (not keys), and payload content. For logs, it should cover the message text and logger name.

## Why This Matters

Without text search, operators must scan large result sets manually or rely on knowing exact structured filter values. Keyword search is a fundamental capability for any monitoring or observability tool, enabling rapid incident investigation and pattern discovery.
