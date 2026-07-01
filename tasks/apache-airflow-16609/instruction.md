Implement the ability to create, update, and delete users through the Airflow REST API. Ensure that the API enforces authentication and authorization, handles errors appropriately, and returns the correct HTTP status codes for various scenarios.

*   Implement the `post_user` function in `airflow/api_connexion/endpoints/user_endpoint.py`:
    *   Create a new user from the JSON request body.
    *   Assign a default role if no roles are specified.
    *   Return a 200 status code on success with the created user as a dict.
    *   Return 401 for unauthenticated requests, 403 for lack of CREATE permission, and 409 if the username already exists.
    *   Handle invalid payloads by returning 400 with a specific error message format.

*   Implement the `patch_user` function in `airflow/api_connexion/endpoints/user_endpoint.py`:
    *   Update an existing user based on the provided `username` and `update_mask`.
    *   Use `generate_password_hash` to hash passwords before storing.
    *   Exclude the password field from the response.
    *   Return a 200 status code with the updated user data.
    *   Return 401 for unauthenticated requests, 403 for lack of EDIT permission, and 404 if the user does not exist.
    *   Handle invalid payloads by returning 400 with a specific error message format.

*   Implement the `delete_user` function in `airflow/api_connexion/endpoints/user_endpoint.py`:
    *   Permanently remove the specified user.
    *   Return a 204 No Content status code on success.
    *   Return 401 for unauthenticated requests, 403 for lack of DELETE permission, and 404 if the user does not exist.

*   Ensure all endpoints handle role validation:
    *   Return 400 with a message for unknown role names in the request body.

*   Import `generate_password_hash` by name in `airflow/api_connexion/endpoints/user_endpoint.py` for use in `patch_user`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.