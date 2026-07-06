Update the semantic conventions library to align with the latest specification by modifying two constants. Ensure the schema URL and messaging attribute identifier reflect the current standards.

*   Update the SCHEMA_URL constant:
    *   File: `opentelemetry-semantic-conventions/src/lib.rs`
    *   Set the value to "https://opentelemetry.io/schemas/1.26.0".
    *   Ensure it is a public constant accessible as the top-level export of the `opentelemetry_semantic_conventions` crate.

*   Update the MESSAGING_CLIENT_ID constant:
    *   File: `opentelemetry-semantic-conventions/src/trace.rs`
    *   Set the value to "messaging.client.id".
    *   Ensure it uses dot separators throughout the name.
    *   Ensure it is a public constant accessible as `opentelemetry_semantic_conventions::trace::MESSAGING_CLIENT_ID`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.