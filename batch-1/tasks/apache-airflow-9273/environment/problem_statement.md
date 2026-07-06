## Description

Airflow's REST API includes a variables resource, but the endpoints for managing variables (key-value pairs used in DAGs) are not actually implemented — they raise "not implemented" errors or are entirely skipped. This means developers and automation tools cannot use the API to read, create, update, or delete variables.

## Expected Behavior

The variables REST API should support the full lifecycle:

- **Fetch a single variable** by key: returns the key and its value, or 404 if not found
- **List all variables** with pagination support (limit and offset): returns a list of variables with their keys and values, plus the total count. The default maximum returned per request should be 100.
- **Create a new variable**: accepts a key and value and stores it; returns the created variable
- **Update an existing variable**: accepts a key and value; returns 204 on success; returns 400 if the key in the request body doesn't match the URL parameter, or if required fields are missing
- **Delete a variable** by key: returns 204 on success, or 404 if the key doesn't exist

## Why This Matters

DAG authors and CI/CD pipelines often need to manage Airflow variables programmatically. Without a working REST API, they are forced to use the UI or the Airflow CLI, which are harder to automate. Completing the variable endpoints makes Airflow's REST API actually usable for variable management.
