Implement a lightweight, built-in authentication manager for Apache Airflow that does not require an external identity provider. This manager should support role-based access control, automatically generate and persist user passwords, and provide a simple web interface for login and logout.

*   Implement `SimpleAuthManagerUser` in `airflow/auth/managers/simple/user.py`:
    *   Accept `username` and `role` as keyword-only parameters in the constructor.
    *   Implement `get_id()` and `get_name()` to return the username.
    *   Implement `get_role()` to return the role.

*   Implement `SimpleAuthManager` in `airflow/auth/managers/simple/simple_auth_manager.py`:
    *   Define a class-level attribute `GENERATED_PASSWORDS_FILE` for the JSON file path storing passwords.
    *   Implement `init()` to manage the `GENERATED_PASSWORDS_FILE`:
        *   Write '{}' if `SIMPLE_AUTH_MANAGER_USERS` is empty or not configured.
        *   Write a JSON dict with one key per configured user if users are set.
    *   Implement `is_logged_in()` to check for a 'user' key in the Flask session.
    *   Implement `get_user()` to return the `SimpleAuthManagerUser` from session['user'] or `None`.
    *   Implement `get_url_login()` and `get_url_logout()` using `url_for` to resolve login/logout views.
    *   Implement authorization methods:
        *   `is_authorized_configuration`, `is_authorized_connection`, `is_authorized_variable`: OP and ADMIN roles for all methods; USER and VIEWER roles denied for write methods.
        *   `is_authorized_dataset`, `is_authorized_pool`: ADMIN, OP, USER, VIEWER roles for GET; only OP and ADMIN for write methods.
        *   `is_authorized_dag`: ADMIN, OP, USER roles for all methods; VIEWER only for GET.
        *   `is_authorized_view`, `is_authorized_custom_view`: True for any logged-in user.
    *   Implement `register_views()` to call `appbuilder.add_view_no_menu()` with `SimpleAuthManagerAuthenticationViews`.

*   Implement `SimpleAuthManagerAuthenticationViews` in `airflow/auth/managers/simple/views/auth.py`:
    *   Expose routes:
        *   GET `/login` to render the login page.
        *   GET/POST `/logout` to clear the session and redirect to `/login`.
        *   GET/POST `/login_submit` to validate credentials and redirect appropriately.

*   Handle session and redirection:
    *   `/logout` endpoint must clear the session and redirect to `/login` with HTTP 302.
    *   `/login_submit` endpoint must validate credentials, set session['user'], and redirect to `url_for('Airflow.index')` on success or back to the login page with an error on failure.

*   Use `SIMPLE_AUTH_MANAGER_USERS` Flask app config for user definitions, matching passwords from `GENERATED_PASSWORDS_FILE`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.