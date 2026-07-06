Implement a fix for the IPC request parsing in a Tauri app using the isolation security pattern. Ensure that the content type embedded within the encrypted payload is used for deserialization, rather than the outer HTTP headers. Additionally, make necessary improvements to support testing and ensure proper handling of binary and structured data.

*   Update the `parse_invoke_request` function in `core/tauri/src/ipc/protocol.rs`:
    *   Extract the content-type from HTTP request headers before decryption.
    *   Override the content-type with the one from the decrypted payload when the isolation pattern is active.
    *   Deserialize the body as `InvokeBody::Raw(Vec<u8>)` for non-JSON MIME types and `InvokeBody::Json(serde_json::Value)` for 'application/json'.
    *   Populate the `InvokeRequest` with `cmd`, `callback`, `error`, `invoke_key`, `url`, and `headers`.

*   Modify the `RawIsolationPayload` struct in `core/tauri-utils/src/pattern/isolation.rs`:
    *   Include a `content_type` field of type `Cow<'a, str>`, deserialized from the JSON field 'contentType'.
    *   Implement a public method `content_type(&self) -> &Cow<'a, str>`.

*   Enhance the `AesGcmPair` struct in `core/tauri-utils/src/pattern/isolation.rs`:
    *   Add a public `encrypt` method with the signature `encrypt(&self, nonce: &[u8; 12], payload: &[u8]) -> Result<Vec<u8>, Error>`.

*   Update the `InvokeBody` enum in `core/tauri/src/ipc/mod.rs`:
    *   Derive `PartialEq` under test configuration using `#[cfg_attr(test, derive(PartialEq))]`.

*   Adjust the `generate_context!` macro in `core/tauri-macros/src/context.rs`:
    *   Accept an optional `test = true` parameter to skip embedding the macOS Info.plist during test compilation.

*   Ensure the `ContextData` struct in `core/tauri-codegen/src/context.rs`:
    *   Includes a `pub test: bool` field to indicate test context.

*   Provide an isolation test fixture application:
    *   Place `tauri.conf.json` in `core/tauri/test/fixture/isolation/src-tauri/` with `security.pattern.use = "isolation"` and `options.dir = "../isolation-dist"`.
    *   Include `index.js` in `core/tauri/test/fixture/isolation/isolation-dist/` defining `window.__TAURI_ISOLATION_HOOK__`.
    *   Ensure `dist/index.html` contains an iframe element.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.