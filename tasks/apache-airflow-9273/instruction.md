Implement the REST API endpoints for managing Airflow variables to support fetching, listing, creating, updating, and deleting variables. Ensure that all endpoints handle errors appropriately and return the correct HTTP status codes and JSON responses.

* Implement the `get_variable` function in `airflow/api_connexion/endpoints/variable_endpoint.py`:
    * Return HTTP 200 with JSON `{"key": "<key>", "value": "<value>"}` if the variable exists.
    * Return HTTP 404 if the variable key does not exist.

* Implement the `delete_variable` function in `airflow/api_connexion/endpoints/variable_endpoint.py`:
    * Return HTTP 204 No Content if the variable is successfully deleted.
    * Return HTTP 404 if the variable key does not exist.

* Implement the `get_variables` function in `airflow/api_connexion/endpoints/variable_endpoint.py`:
    * Accept 'limit' and 'offset' query parameters.
    * Return HTTP 200 with JSON `{"variables": [{"key": str, "value": str}], "total_entries": int}`.
    * Default to a maximum of 100 variables returned per request, regardless of the total number of variables.

* Implement the `patch_variable` function in `airflow/api_connexion/endpoints/variable_endpoint.py`:
    * Accept a JSON body with 'key' and 'value' fields.
    * Return HTTP 204 No Content on successful update.
    * Return HTTP 400 with JSON `{"title": "Invalid post body", "status": 400, "type": "about:blank", "detail": "key from request body doesn't match uri parameter"}` if the 'key' field in the request body does not match the URL key.
    * Return HTTP 400 with JSON `{"title": "Invalid Variable schema", "status": 400, "type": "about:blank", "detail": "{'value': ['Missing data for required field.']}"}` if the request body is missing the 'value' field.

* Implement the `post_variables` function in `airflow/api_connexion/endpoints/variable_endpoint.py`:
    * Accept a JSON body with 'key' and 'value' fields.
    * Create the variable and return HTTP 200 with JSON `{"key": str, "value": str}`.
    * Return HTTP 400 with JSON `{"title": "Invalid Variable schema", "status": 400, "type": "about:blank", "detail": "{'value': ['Missing data for required field.']}"}` if the request body is missing the 'value' field.

* Update `VariableSchema` in `airflow/api_connexion/schemas/variable_schema.py`:
    * Ensure it has required fields "key" (string) and "value" (string, mapped to the model's "val" attribute).
    * Create a singleton instance `variable_schema` with `strict=True`.

* Update `VariableCollectionSchema` in `airflow/api_connexion/schemas/variable_schema.py`:
    * Ensure it has fields "variables" (list of `VariableSchema`) and "total_entries" (integer).
    * Create a singleton instance `variable_collection_schema` with `strict=True`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.