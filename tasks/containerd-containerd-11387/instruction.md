Update the HTTP/2 networking library used by the project to ensure it recognizes the extended connect protocol setting by its standardized name. This will enable proper support for tunneling non-HTTP protocols over HTTP/2.

*   Update the project's HTTP/2 networking dependency:
    *   Ensure the version used recognizes HTTP/2 SettingID value 0x8 as "ENABLE_CONNECT_PROTOCOL".
*   Modify the string representation behavior:
    *   Ensure that when the string representation of HTTP/2 SettingID 0x8 is requested, it returns "ENABLE_CONNECT_PROTOCOL".
*   Update project files:
    *   Modify `go.mod` and `go.sum` to reflect the updated version of the HTTP/2 library.
    *   If applicable, update the `vendor` directory to include the correct version of the `golang.org/x/net/http2` package that supports the ENABLE_CONNECT_PROTOCOL SettingID.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.