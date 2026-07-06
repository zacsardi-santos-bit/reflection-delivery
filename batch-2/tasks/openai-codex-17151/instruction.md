I'm working on adding support for government-compliance-enrolled accounts in our API client library.

*   The CoreAuthProvider struct must include a public boolean field named is_fedramp_account that indicates whether the account requires government-compliance routing. The field must be accessible as a plain struct field.

*   The CoreAuthProvider struct must be constructable with explicit values for token, account_id, and is_fedramp_account fields simultaneously.

*   A function add_fedramp_routing_header must exist in codex-rs/codex-api/src/auth.rs with the signature add_fedramp_routing_header(headers: &mut HeaderMap). When called, it must insert the HTTP header X-OpenAI-Fedramp with the value "true" into the provided header map.

*   When CoreAuthProvider.is_fedramp_account is true, calling add_auth_headers on that provider must result in the X-OpenAI-Fedramp header being set to "true" in the provided HeaderMap.


*   Interface details: Type: Struct
Name: CoreAuthProvider
Location: codex-rs/codex-api/src/api_bridge.rs
Description: Authentication provider for core API requests. Must have three public fields: token (Option<String>), account_id (Option<String>), and a new field is_fedramp_account (bool). The is_fedramp_account field must be pub and directly readable on instances of the struct.
Signature: CoreAuthProvider { token: Option<String>, account_id: Option<String>, is_fedramp_account: bool }

Type: Function
Name: add_fedramp_routing_header
Location: codex-rs/codex-api/src/auth.rs
Signature: add_fedramp_routing_header(headers: &mut HeaderMap)
Description: Inserts the HTTP header X-OpenAI-Fedramp with the static value "true" into the provided HeaderMap. This function is pub(crate). When CoreAuthProvider.is_fedramp_account is true, CoreAuthProvider's add_auth_headers implementation must call this function to attach the FedRAMP routing header.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.