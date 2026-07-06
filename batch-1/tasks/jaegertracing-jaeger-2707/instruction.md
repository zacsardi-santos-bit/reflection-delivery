Extend the Jaeger Query service's static file handler to support JavaScript configuration files for the web UI, in addition to the existing JSON support. Implement the ability to read JavaScript files, validate that they define the required configuration function, and inject them into the page correctly. Ensure error messages are clear and informative for unsupported formats and missing configurations.

*   Implement the `loadUIConfig` function in `cmd/query/app/static_handler.go` with the following behavior:
    *   Return `nil, nil` when the `uiConfig` string is empty.
    *   For a `.json` file path:
        *   Parse the JSON.
        *   Return a `*loadedConfig` with `config` set to `[]byte("JAEGER_CONFIG = " + marshaled_json + ";")`.
        *   Set `regexp` to `configPattern`.
    *   For a `.js` file path containing `function UIConfig()`:
        *   Return a `*loadedConfig` with `config` set to the trimmed file bytes.
        *   Set `regexp` to `configJsPattern`.
    *   For a `.js` file path without `function UIConfig()`:
        *   Return an error: "UI config file must define function UIConfig(): <path>".
    *   For unsupported file extensions:
        *   Return an error: "unrecognized UI config file format, expecting .js or .json file: <path>".
    *   For nonexistent or unreadable files:
        *   Return an error: "cannot read UI config file <path>: open <path>: no such file or directory".

*   Update the `index.html` fixture in `cmd/query/app/fixture/index.html`:
    *   Include a comment block with "// JAEGER_CONFIG_JS" followed by explanatory lines.
    *   Include the line "JAEGER_CONFIG=DEFAULT_CONFIG;" within the comment block.

*   Create fixture files in `cmd/query/app/fixture/`:
    *   `ui-config.js`: Defines `function UIConfig()` returning `{x: "y"}`.
    *   `ui-config-malformed.js`: Does not define `function UIConfig()`, contains an arrow function instead.
    *   `ui-config-menu.js`: Defines `function UIConfig()` with a menu entry.

*   Ensure the `configJsPattern` variable in `cmd/query/app/static_handler.go`:
    *   Is a compiled `*regexp.Regexp`.
    *   Matches the "// JAEGER_CONFIG_JS" comment line and the following line.
    *   Uses case-insensitive and multiline flags.

*   Ensure the `configPattern` variable matches the JSON config placeholder "JAEGER_CONFIG *= *DEFAULT_CONFIG;".

*   When the static handler is configured with no `UIConfig` path:
    *   The served HTML must contain "JAEGER_CONFIG=DEFAULT_CONFIG;".

*   When configured with a `.json` `UIConfig` path:
    *   The served HTML must contain the JSON config formatted as "JAEGER_CONFIG = {<json content>};".

*   When configured with a `.js` `UIConfig` path:
    *   The served HTML must contain the JavaScript function content starting with "function UIConfig(){".

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.