## Description

When configuring database connections in the SQL kernel, users are required to hardcode all connection parameters — including credentials — directly in the connection URL. There is no way to reference shared notebook variables when setting up a data source, meaning sensitive information like usernames and passwords cannot be managed dynamically.

## Expected Behavior

- Users should be able to reference shared notebook variables inside database connection strings using a placeholder syntax.
- When a connection string is set (for either a named data source or the default data source), any placeholders referencing notebook variables should be automatically resolved to their current runtime values before the connection is established.
- Variables set in one notebook kernel (e.g., Groovy) should be retrievable when the SQL kernel resolves these connection string placeholders.

## Why This Matters

This allows teams to avoid hardcoding credentials in notebooks. A user can store a database username and password as shared variables — loaded from a file or other secure source — and have the SQL connection string reference them by name. The credentials are then resolved automatically at connection time, keeping notebooks free of sensitive hardcoded values and making it easy to switch between environments.
