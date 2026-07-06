I'm working with Airflow's REST API and noticed that the variables endpoints are completely non-functional. The stubs exist (delete, get, list, patch, and create), but they all raise "not implemented" errors. I need them to actually work so I can manage Airflow variables programmatically.

Specifically, I need:
- Fetching a single variable by key to return its key and value as JSON, and return a 404 when it doesn't exist
- Listing variables to support limit and offset query parameters with a default cap of 100, and to return both the variable list and the total count
- Creating a variable to return the created variable; posting with a missing required field should return a 400 error
- Updating a variable to return 204 on success; if the key in the request body doesn't match the URL key, it should return a 400 with a message saying the keys don't match; if required fields are missing, it should also return a 400
- Deleting a variable to return 204 on success and 404 if the key doesn't exist

The error responses for validation failures should include a title, status, type, and detail field. For schema validation failures (missing required fields), the detail should reflect exactly what field validation failed. For key mismatch in updates, the detail should explain that the body key doesn't match the URL parameter.
