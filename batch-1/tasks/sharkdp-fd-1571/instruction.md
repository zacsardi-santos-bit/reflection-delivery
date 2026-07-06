Implement a feature in the `fd` command-line tool to support terminal hyperlinks for output file paths. This feature should be controlled by a new command-line option, allowing users to specify when hyperlinks should be included in the output.

*   Add a new command-line argument `--hyperlink=<when>` to the `fd` tool.
    *   Accept values: 'always', 'never', 'auto'.
    *   Default value is 'never'.
    *   If provided without a value, default to 'auto'.
    *   Alias: `--hyper`.

*   Implement hyperlink output using OSC 8 terminal escape sequences.
    *   Format: '\x1b]8;;{url}\x1b\\' followed by the file path, ending with '\x1b]8;;\x1b\\'.
    *   URL format: 'file://{hostname}{absolute_path}'.
        *   On Unix systems, include the system hostname.
        *   On non-Unix systems, omit the hostname, resulting in 'file:///path/to/file'.

*   Modify the `Opts` struct in `src/cli.rs` to include the `--hyperlink` option.
    *   Use the `HyperlinkWhen` enum to represent the option values.

*   Create a new module `src/hyperlink.rs` to handle URL formatting.
    *   Implement a `PathUrl` struct to wrap paths and format them as file URLs.
    *   Ensure `PathUrl` implements `Display` for URL string generation.
    *   Use `PathUrl::new(path: &Path) -> Option<PathUrl>` to create instances.
    *   Percent-encode path bytes that are not unreserved characters.

*   Update the `Config` struct in `src/config.rs` to include a `hyperlink` field.
    *   Set `hyperlink` to true for `HyperlinkWhen::Always`.
    *   Set `hyperlink` to false for `HyperlinkWhen::Never`.
    *   For `HyperlinkWhen::Auto`, follow the `colored_output` flag.

*   Ensure the `LS_COLORS` environment variable is cleared in the test environment to maintain consistent output.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.