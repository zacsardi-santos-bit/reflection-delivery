I'm working on the marimo notebook server and I'd like to add language server workspace context to the page configuration that gets embedded in every served notebook page.

*   DEFAULT_NOTEBOOK_NAME must be exported as a module-level string constant from marimo/_server/api/endpoints/assets.py. It serves as the fallback notebook filename when computing LSP workspace document URIs in the absence of a specific notebook file.

*   notebook_page_template in marimo/_server/templates/templates.py must accept an optional lsp_workspace keyword argument (a dict with string fields rootUri and documentUri, or None). This parameter must be serialized into the window.__MARIMO_MOUNT_CONFIG__ JSON block in the returned HTML as the key lspWorkspace (camelCase). When lsp_workspace is None, the JSON must include 'lspWorkspace': null.

*   When lsp_workspace is provided with rootUri and documentUri string values, the HTML output of notebook_page_template must contain those values embedded in the mount config JSON, e.g. '"rootUri": "<value>"' and '"documentUri": "<value>"' must appear in the result.

*   The GET / index endpoint must compute and include an lspWorkspace value in the HTML mount config for every notebook page served. When the file router is set to a specific notebook file, rootUri must be the file URI of the nearest ancestor directory containing a pyproject.toml (searched upward from the notebook file's parent directory), and documentUri must be the file URI of the notebook file itself.

*   When the file router is configured with a directory and no specific notebook file is selected, rootUri must be the file URI of the nearest ancestor directory containing a pyproject.toml (searched upward from that directory), and documentUri must be the file URI of the directory joined with DEFAULT_NOTEBOOK_NAME.

*   When no directory or file is available (e.g., new file mode with no directory context), rootUri must fall back to the current working directory as a file URI, and documentUri must be the current working directory joined with DEFAULT_NOTEBOOK_NAME as a file URI.

*   The export snapshot template files (used by static export) must include 'lspWorkspace': null in the window.__MARIMO_MOUNT_CONFIG__ JSON block.

*   file_router_scope must be exported from tests/_server/mocks.py as a context manager function. It accepts a TestClient and an AppFileRouter, temporarily replaces the session manager's file_router for the duration of the context, then restores the original. The with_file_router decorator must use file_router_scope internally.


*   Interface details: Type: Constant
Name: DEFAULT_NOTEBOOK_NAME
Location: marimo/_server/api/endpoints/assets.py
Description: A string constant representing the default notebook filename used when no specific notebook file is set. Used as the fallback document path when computing LSP workspace information.

Type: Function
Name: notebook_page_template
Location: marimo/_server/templates/templates.py
Signature: notebook_page_template(..., lsp_workspace: Optional[dict] = None, ...) -> str
Description: Renders the HTML page for a notebook. Now accepts an optional `lsp_workspace` keyword argument — a dict with `rootUri` (str) and `documentUri` (str) keys, or None. When provided, the values are included in the `window.__MARIMO_MOUNT_CONFIG__` JSON embedded in the returned HTML as a `lspWorkspace` (camelCase) field. When None, `lspWorkspace` is null in the JSON output.

Type: Function
Name: file_router_scope
Location: tests/_server/mocks.py
Signature: file_router_scope(client: TestClient, file_router: AppFileRouter) -> Iterator[None]
Description: A context manager that temporarily replaces the session manager's file router on the given test client with the provided file_router, then restores the original router when the context exits. Used in tests to set up file router state without decorators.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.