I'm working on the Snowflake database connection feature and need to add support for multiple authentication methods.

*   The Snowflake DatabaseConnection type must replace the top-level 'username' and 'password' fields with an 'authType' discriminated union field (discriminant key: 'type'), supporting four variants: 'Password', 'SSO (Browser)', 'Key Pair', and 'OAuth / PAT'.

*   The 'Password' variant of authType must include 'username' (string), 'password' (string), and 'enable_mfa' (boolean, default false). When enable_mfa is false, the generated Python code must produce a standard SQLModel/SQLAlchemy engine with URL params in order: account, database, warehouse (if set), schema (if set), role (if set), user, password. The password must be read from the environment variable 'SNOWFLAKE_PASSWORD'.

*   When the 'Password' auth variant has enable_mfa set to true, the generated Python code must include connect_args={"authenticator": "username_password_mfa"} as an additional argument to create_engine.

*   The 'SSO (Browser)' variant of authType must include 'username' (string). The generated Python code must produce URL params in order: account, database, warehouse (if set), schema (if set), role (if set), user, authenticator="externalbrowser". No connect_args should be added, and no 'import os' statement should appear when no secrets are present.

*   The 'Key Pair' variant of authType must include 'username' (string), 'private_key_path' (string), and 'private_key_passphrase' (optional string). The generated Python code must produce URL params with: account, database, warehouse (if set), schema (if set), role (if set), user — plus connect_args with 'authenticator': 'SNOWFLAKE_JWT' and 'private_key_file'. The passphrase must be read from env var 'SNOWFLAKE_PRIVATE_KEY_PASSPHRASE'. When no passphrase is provided, 'private_key_file_pwd' must be omitted entirely from connect_args.

*   The 'OAuth / PAT' variant of authType must include 'token' (string). The generated Python code must produce URL params in order: account, database, warehouse (if set), schema (if set), role (if set), authenticator="oauth", token. No 'user' field should appear. The token must be read from env var 'SNOWFLAKE_TOKEN'.

*   When a Snowflake connection has multiple secret/environment-variable-backed fields, the generated Python variable declarations (e.g. '_account = os.environ.get(...)') must appear in alphabetical order of the variable name.

*   The URL parameters for all Snowflake auth types must follow the order: account first, then database, warehouse (if set), schema (if set), role (if set), then auth-specific parameters (user, password, authenticator, token, etc.).


*   Interface details: ## Interfaces Required

### Modified Type: Snowflake DatabaseConnection variant
**Location:** `frontend/src/components/editor/connections/database/schemas.ts`

The `DatabaseConnection` type's Snowflake variant must replace the top-level `username` and `password` fields with an `authType` discriminated union field. The discriminant key is `type`.

The `authType` field must accept one of four variants:

**Variant 1 — Password**
- `type: "Password"`
- `username: string`
- `password: string`
- `enable_mfa: boolean` (default `false`)

**Variant 2 — SSO (Browser)**
- `type: "SSO (Browser)"`
- `username: string`

**Variant 3 — Key Pair**
- `type: "Key Pair"`
- `username: string`
- `private_key_path: string`
- `private_key_passphrase?: string` (optional)

**Variant 4 — OAuth / PAT**
- `type: "OAuth / PAT"`
- `token: string`

The top-level Snowflake connection fields `account`, `database`, `warehouse`, `schema`, and `role` remain unchanged (warehouse, schema, and role are optional strings).

---

### Modified Function: generateDatabaseCode (Snowflake code generation)
**Location:** `frontend/src/components/editor/connections/database/as-code.ts`

The existing `generateDatabaseCode` function must be updated to handle the new `authType` discriminated union on Snowflake connections. The function signature itself does not change; the internal handling of Snowflake connections must be updated.

**Generated code structure per auth type:**

**Password (enable_mfa = false):**
URL params order: `account`, `database`, `warehouse` (if set), `schema` (if set), `role` (if set), `user`, `password`.
Password is sourced from env var `SNOWFLAKE_PASSWORD`.
No `connect_args`.

**Password (enable_mfa = true):**
Same URL params as Password, plus `connect_args={"authenticator": "username_password_mfa"}`.

**SSO (Browser):**
URL params order: `account`, `database`, `warehouse` (if set), `schema` (if set), `role` (if set), `user`, `authenticator="externalbrowser"`.
No `connect_args`. No `import os` (no secrets needed).

**Key Pair:**
URL params order: `account`, `database`, `warehouse` (if set), `schema` (if set), `role` (if set), `user`.
`connect_args` includes: `"authenticator": "SNOWFLAKE_JWT"`, `"private_key_file": <path>`, and `"private_key_file_pwd": <passphrase_var>` only if `private_key_passphrase` is provided.
Passphrase is sourced from env var `SNOWFLAKE_PRIVATE_KEY_PASSPHRASE`.
When no passphrase is provided, `private_key_file_pwd` is omitted entirely from `connect_args`.

**OAuth / PAT:**
URL params order: `account`, `database`, `warehouse` (if set), `schema` (if set), `role` (if set), `authenticator="oauth"`, `token`.
Token is sourced from env var `SNOWFLAKE_TOKEN`. No `user` field.

**Multiple secrets (env var declaration ordering):**
When multiple fields are secrets, the generated env var variable declarations (`_varname = os.environ.get(...)`) appear in alphabetical order of the variable name.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.