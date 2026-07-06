## Description

The Big Number with Trendline chart currently sends two separate queries to the server every time an aggregation method is configured: one query fetches the time series data for the trendline, and a second query fetches the aggregated value displayed as the headline number. For most aggregation methods — such as sum, average, median, minimum, maximum, and last value — this second query is entirely unnecessary because the aggregated value can be calculated directly in the browser from the trendline data that has already been retrieved.

There is also no shared definition of the available aggregation methods and their computation logic, making it difficult to reuse that logic between the query-building and rendering stages of the chart.

Finally, the SQL viewer's format toggle switch displays a static label ("Show original SQL") regardless of the current display mode, making it unclear whether the SQL is currently shown in formatted or original form.

## Expected Behavior

- For most aggregation methods, the chart should send only one query to the server and compute the headline number client-side from the trendline data.
- Only one specific aggregation mode (server-side/overall value) should require a second server query.
- The available aggregation methods and their computation functions should be defined in a single shared location within the chart-controls package so they can be used both during query building and during rendering.
- The aggregation method keys should be consistently cased (lowercase) across the codebase.
- When a valid aggregated value is successfully computed from the trendline data, the fallback value field should be null rather than set to a non-null fallback.
- The SQL viewer format toggle should display "formatted" when showing formatted SQL and "original" when showing the raw SQL, making the current state clear to users.

## Why This Matters

Eliminating redundant server queries improves chart load performance. Centralizing aggregation logic reduces duplication and makes it easier to add or modify aggregation methods in the future. The accessible toggle label improvement benefits all users, especially those relying on screen readers or other assistive tools.
