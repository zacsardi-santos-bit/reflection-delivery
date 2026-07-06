Implement a new API endpoint that allows authenticated users to retrieve a list of their active tokens and rename an existing database method for clarity. Ensure the new endpoint provides detailed information about each token, including its type, and update the database method to reflect its specific functionality.

*   Rename the database method:
    *   Change the method name from `get_token` to `get_token_by_hash` in `api/src/db/database.rs`.
    *   Update all call sites to use `get_token_by_hash`, including the authentication middleware in `api/src/util.rs`.
    *   Ensure `get_token_by_hash` accepts a `hash` string and returns `Ok(Some(Token))` if a matching token is found, or `Ok(None)` if not.

*   Implement a new database method:
    *   Create `list_tokens` in `api/src/db/database.rs` with the signature `pub async fn list_tokens(&self, user_id: Uuid) -> Result<Vec<Token>>`.
    *   Ensure it returns all tokens for the specified user as `Vec<Token>`.

*   Define a new enum for token types:
    *   Create `ApiTokenType` in `api/src/api/types.rs` with variants `Web`, `Device`, and `Personal`.
    *   Ensure it serializes and deserializes using snake_case (e.g., "web", "device", "personal").

*   Define a new struct for API tokens:
    *   Create `ApiToken` in `api/src/api/types.rs` with fields: `id` (Uuid), `user_id` (Uuid), `type` (ApiTokenType), `description` (Option<String>), `expires_at` (Option<DateTime<Utc>>), `updated_at` (DateTime<Utc>), and `created_at` (DateTime<Utc>).
    *   Ensure fields are serialized in camelCase and implement `From<Token>`.

*   Register a new HTTP route:
    *   Add a handler for `GET /api/user/tokens` in `api/src/api/self_user.rs`.
    *   Ensure it requires authentication and returns a JSON array of `ApiToken` objects for the current user.
    *   Register this route in the `self_user_router()` function at the path `/tokens`.

*   Add a new token type:
    *   Introduce a `Personal` variant to the existing `TokenType` enum in `api/src/db/models.rs`.

*   Verify functionality:
    *   When a test user with a single active web session token calls `GET /api/user/tokens`, ensure the response is a JSON array containing exactly one element with a `type` field equal to "web".

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.