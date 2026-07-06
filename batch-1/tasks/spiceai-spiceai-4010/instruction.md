Implement an API key system with distinct read-only and read-write access levels to secure the flight data ingestion endpoint. Ensure that only authenticated requests with appropriate access levels are allowed to proceed.

*   Define an `ApiKey` enum in `crates/spicepod/src/component/runtime.rs` with two variants:
    *   `ReadOnly { key: String }`
    *   `ReadWrite { key: String }`
    *   Ensure `ApiKey` implements `Clone`, `Debug`, `PartialEq`, and `PartialEq<str>`.

*   Implement `ApiKey::parse_str` in `crates/spicepod/src/component/runtime.rs`:
    *   Parse input strings to determine access level.
    *   Input ending with `:rw` should return `ApiKey::ReadWrite` with the key portion before `:rw`.
    *   Input ending with `:ro` or with no recognized suffix should return `ApiKey::ReadOnly`.
    *   Input with an unrecognized suffix (e.g., `foo:bar`) should treat the entire input as the key.

*   Update `ApiKeyAuth::new` in `crates/runtime-auth/src/api_key/mod.rs`:
    *   Accept `Vec<ApiKey>` instead of `Vec<String>`.
    *   Ensure existing authentication logic (HTTP, Flight, gRPC) remains functional with the new `ApiKey` type.

*   Modify the flight data ingestion endpoint (do_put handler):
    *   Reject unauthenticated requests with an error.
    *   Reject requests authenticated with a read-only key with an error.
    *   Allow requests authenticated with a read-write key to proceed.

*   Implement `EndpointAuth::with_flight_basic_auth` in `crates/runtime/src/auth/mod.rs`:
    *   Accept an `Arc<dyn FlightBasicAuth + Send + Sync>`.
    *   Return an updated `EndpointAuth` configured with the provided flight authentication handler.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.