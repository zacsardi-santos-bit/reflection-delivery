I'm working on the server-side HTML template rendering for exported notebooks.

*   The static_notebook_template function in marimo/_server/templates/templates.py must inject a '<script data-marimo="true">' block into the rendered HTML that sets window.__MARIMO_EXPORT_CONTEXT__ using Object.defineProperty, with writable: false and configurable: false

*   The wasm_notebook_template function in marimo/_server/templates/templates.py must also inject the same __MARIMO_EXPORT_CONTEXT__ script block into the rendered HTML output

*   The __MARIMO_EXPORT_CONTEXT__ value must be created with Object.freeze and contain exactly two fields: 'trusted' (the literal boolean true) and 'notebookCode' (the notebook source code as a JSON-encoded string)

*   The notebookCode field must equal the JSON-encoded value of the 'code' parameter passed to the template function; when code is an empty string, notebookCode must be the empty JSON string ""

*   For static_notebook_template, the export context script block must appear after the window.__MARIMO_STATIC__ script block and before </head>

*   For wasm_notebook_template, the export context script block must appear before the <marimo-code hidden=""> element (which is itself placed before </head>)

*   The rendered output from both template functions must exactly match the provided snapshot files (export1.txt through export6.txt after normalization): the script block uses 4-space indentation for the Object.defineProperty call, 8-space indentation for value/writable/configurable, and 12-space indentation for trusted/notebookCode

*   The static_notebook_template output must contain the '<marimo-code hidden="">' element (existing behavior that must be preserved)


*   Interface details: Type: Function
Name: static_notebook_template
Location: marimo/_server/templates/templates.py
Signature: static_notebook_template(html, user_config, config_overrides, server_token, app_config, filename, code, code_hash, session_snapshot, notebook_snapshot, files) -> str
Description: Renders a self-contained static notebook HTML page. Must be modified to inject the __MARIMO_EXPORT_CONTEXT__ script block after the window.__MARIMO_STATIC__ block. The notebookCode in the export context must be the JSON-encoded value of the `code` parameter. The rendered output must contain '<marimo-code hidden="">'.

Type: Function
Name: wasm_notebook_template
Location: marimo/_server/templates/templates.py
Signature: wasm_notebook_template(html, version, filename, mode, user_config, config_overrides, app_config, code, show_code, asset_url=None) -> str
Description: Renders a WebAssembly-powered notebook HTML page. Must be modified to inject the __MARIMO_EXPORT_CONTEXT__ script block before the <marimo-code hidden=""> element (which itself appears before </head>).

Type: Function
Name: _export_context_block
Location: marimo/_server/templates/templates.py
Signature: _export_context_block(*, notebook_code: str) -> str
Description: Helper that returns the <script data-marimo="true"> block embedding the __MARIMO_EXPORT_CONTEXT__ object. The block must produce exactly this indentation and structure (as enforced by the snapshot tests):

    <script data-marimo="true">
        Object.defineProperty(window, "__MARIMO_EXPORT_CONTEXT__", {
            value: Object.freeze({
                trusted: true,
                notebookCode: <json_encoded_notebook_code>,
            }),
            writable: false,
            configurable: false,
        });
    </script>

The notebookCode value must be produced by JSON-encoding the notebook_code string (equivalent to json.dumps). The block begins with a newline and ends with a newline. The 4-space indentation inside the script tag is the common indentation level used in the snapshot files.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.