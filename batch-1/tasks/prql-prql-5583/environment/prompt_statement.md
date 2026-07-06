I'm using PRQL to compile queries targeting Snowflake and running into issues when I use grouping with record selection. When I write a query that groups records by a field and picks the first one per group — without specifying any sort order — the PRQL compiler generates a window function in the SQL that is missing an ORDER BY clause. Snowflake requires ranking-style window functions to always have an ORDER BY in the window specification, so the generated SQL gets rejected.

I'd expect the Snowflake SQL backend to automatically include a fallback ordering (like ordering by a constant) in the window specification when no sort is specified, so that the output SQL is valid. When I do specify an explicit sort inside the group expression, I'd expect that sort to be used as-is — no fallback needed in that case.

Could you update the SQL generation logic so that the Snowflake dialect adds this fallback ORDER BY when one isn't already present?
