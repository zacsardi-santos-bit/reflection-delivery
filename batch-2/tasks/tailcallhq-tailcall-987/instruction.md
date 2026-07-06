Replace the existing JWT library in your project with the SurrealDB-compatible fork to ensure compatibility with the SurrealDB ecosystem. Update all relevant imports and adjust for any API changes to maintain current JWT authentication functionality.

*   Modify the `Cargo.toml` file:
    *   Remove or comment out the line: `jsonwebtoken = "9.2.0"`.
    *   Add the line: `surrealdb-jsonwebtoken = "8.3.0-surreal.1"`.

*   Update import paths in the following source files:
    *   `src/auth/jwt/jwks.rs`:
        *   Change `use jsonwebtoken::jwk::{Jwk, JwkSet};` to `use surrealdb_jsonwebtoken::jwk::{Jwk, JwkSet};`.
        *   Change `use jsonwebtoken::{decode, decode_header, Algorithm, DecodingKey, Validation};` to `use surrealdb_jsonwebtoken::{decode, decode_header, DecodingKey, Validation};`.
        *   Replace algorithm retrieval logic with `jwk.common.algorithm.ok_or(Error::ValidationCheckFailed)?`.
        *   Remove `validation.validate_aud = false;` and `use std::str::FromStr;`.
    *   `src/auth/jwt/jwks_remote.rs`:
        *   Change `use jsonwebtoken::jwk::JwkSet;` to `use surrealdb_jsonwebtoken::jwk::JwkSet;`.
    *   `src/auth/jwt/jwt_verify.rs` (in tests module):
        *   Change `use jsonwebtoken::jwk::JwkSet;` to `use surrealdb_jsonwebtoken::jwk::JwkSet;`.
    *   `src/blueprint/from_config/auth.rs`:
        *   Change `use jsonwebtoken::jwk::JwkSet;` to `use surrealdb_jsonwebtoken::jwk::JwkSet;`.

*   Ensure `surrealdb_jsonwebtoken::jwk::JwkSet`:
    *   Implements `serde::Deserialize`.
    *   Has a `keys` field that can deserialize from a JSON object `{"keys": []}` into an instance where `.keys.is_empty()` returns `true`.

*   Maintain existing JWT authentication behaviors:
    *   Token decoding with JWKS.
    *   Issuer (iss) claim validation.
    *   Audience (aud) claim validation for both single and multiple audiences.
    *   Key ID (kid) matching (required and optional).
    *   Request-level auth context validation.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.