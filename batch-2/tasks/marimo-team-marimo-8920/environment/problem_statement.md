## Description

The SQL engine's database introspection logic is fragile: if any metadata-fetching call (schemas, tables, columns, primary keys, indexes) fails, the entire database browsing feature breaks rather than gracefully degrading. Additionally, the engine only ever looks at a single "default" database, making it impossible to browse other databases accessible to the connection. For cloud data warehouses that require switching databases before introspecting them, there is no mechanism to do so, meaning schema and table discovery silently returns nothing or crashes.

## Expected Behavior

- Metadata-fetching operations (retrieving schemas, tables, columns, primary keys, indexes) should return sensible empty defaults rather than propagating exceptions to the user.
- Failed operations should be logged at an appropriate level (debug, warning, or error) so engineers can diagnose issues without surfacing raw errors to users. Certain expected or harmless exception types should be suppressible entirely without logging.
- The engine should support discovering multiple databases accessible to the connection, not just the configured default.
- For cloud data warehouse dialects that require an explicit "use this database" command before inspecting it, the engine should issue that command automatically when browsing a specific database's schemas and tables.
- Database name discovery for those dialects should normalize names consistently (lowercasing simple identifiers, preserving names containing special characters), and should respect the default database setting when present.

## Why This Matters

Without these changes, any transient network hiccup or minor incompatibility in the database driver crashes schema browsing entirely. Users connecting to cloud data warehouses cannot see any schemas or tables at all because the necessary database-context switch is never issued. Fixing this makes the database exploration sidebar robust and usable across a broader range of database backends.
