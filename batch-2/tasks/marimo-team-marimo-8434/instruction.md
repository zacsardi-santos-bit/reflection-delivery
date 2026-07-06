I'm embedding marimo into a larger web application where I mount the marimo ASGI app at an outer sub-path while the dynamic notebook directory is configured with a different inner path.

*   DynamicDirectoryMiddleware must raise ValueError with a message containing 'non-empty path' when base_path is '/' or '' (empty string).

*   When DynamicDirectoryMiddleware is mounted by an outer framework at a sub-path (e.g., mount_path='/server2') while base_path='/apps', a GET request to the combined URL (e.g., /server2/apps/test_app/) must return HTTP 200.

*   When DynamicDirectoryMiddleware is mounted at a sub-path and the request URL is missing a trailing slash (e.g., /server2/apps/test_app), the middleware must return HTTP 307 with a Location header containing the full correct path including the mount prefix and a trailing slash (e.g., /server2/apps/test_app/).

*   After a notebook app has been loaded at a sub-mount path, asset requests within that notebook (e.g., /server2/apps/test_app/assets/bundle.js) must return HTTP 200.

*   When DynamicDirectoryMiddleware with different base_path and mount_path values serves a notebook, the base_url argument passed to the app_builder callback must be a URL path (not a filesystem path) in the form '{root_path}{base_path}/{relative_notebook_name}' (e.g., '/server2/apps/test_app').

*   When DynamicDirectoryMiddleware is used without an outer mount (used directly), the base_url passed to app_builder must still be a proper URL path formed from the base_path and notebook name (e.g., '/apps/test_app').

*   When DynamicDirectoryMiddleware is mounted at a sub-path, the ASGI scope passed to the inner app created by app_builder must have root_path set to '' (empty string).

*   Nested-directory notebooks must be reachable through sub-path mounts: a notebook at nested/nested_app.py with base_path='/apps' mounted at '/server2' must respond HTTP 200 for /server2/apps/nested/nested_app/.

*   create_asgi_app().with_dynamic_directory(path='/apps', directory=...).build() when mounted at '/server2' in an outer Starlette or FastAPI app must correctly route requests to /server2/apps/{notebook_name}/ with HTTP 200.


*   Interface details: Type: Class
Name: DynamicDirectoryMiddleware
Location: marimo/_server/asgi.py
Description: ASGI middleware that dynamically serves marimo notebook files from a directory. It intercepts requests matching a configured base_path and dispatches them to per-notebook sub-apps created by app_builder.
Signature:
  __init__(self, app: ASGIApp, base_path: str, directory: str, app_builder: Callable[[str, str], ASGIApp]) -> None
  - Raises ValueError (message containing "non-empty path") when base_path is "/" or ""
  __call__(self, scope: dict, receive: Callable, send: Callable) -> Awaitable[None]
  - Must correctly handle sub-path mounting (where outer framework sets scope["root_path"])
  - base_url passed to app_builder must be a URL path of the form "{root_path_prefix}/{relative_notebook_name}"
  - When dispatching to the inner app, scope["root_path"] must be set to ""

Type: Function
Name: create_asgi_app
Location: marimo/_server/asgi.py (exported via marimo/__init__.py)
Description: Factory function that creates an ASGI app builder. Supports method chaining with .with_dynamic_directory() and .build().
Signature: create_asgi_app(quiet: bool = False, include_code: bool = False) -> ASGIAppBuilder
  - .with_dynamic_directory(path: str, directory: str) -> ASGIAppBuilder
  - .build() -> ASGIApp


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.