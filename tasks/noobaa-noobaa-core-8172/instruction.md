Implement input validation for IAM operations to ensure malformed requests are rejected early with descriptive errors. Create reusable validation functions for paths, usernames, access key IDs, markers, and status values. Introduce a constants module for centralized field name strings used in validation messages.

*   Create a new constants module:
    *   File: `src/endpoint/iam/iam_constants.js`
    *   Export string constants: `IAM_PATH`, `USERNAME`

*   Implement validation utility functions in `src/endpoint/iam/iam_utils.js`:
    *   `validate_iam_path(path, field_name?) -> undefined | true`
        *   Return `undefined` if `path` is `undefined`.
        *   Return `true` if `path` equals `IAM_DEFAULT_PATH` or is a valid string (1–512 chars, starts/ends with '/').
        *   Throw `IamError` with `ValidationError.code` for invalid types, empty strings, strings >512 chars, or not starting/ending with '/'.
    *   `validate_username(username, field_name) -> undefined | true`
        *   Return `undefined` if `username` is `undefined`.
        *   Return `true` for valid usernames (1–64 chars, valid char set, not 'anonymous').
        *   Throw `IamError` with `ValidationError.code` for invalid types, empty strings, >64 chars, invalid chars, or reserved names.
    *   `validate_marker(marker) -> undefined | true`
        *   Return `undefined` if `marker` is `undefined`.
        *   Return `true` for valid markers (min 1 char, valid char set).
        *   Throw `IamError` with `ValidationError.code` for invalid types, empty strings, or invalid chars.
    *   `validate_access_key_id(access_key_id) -> undefined | true`
        *   Return `undefined` if `access_key_id` is `undefined`.
        *   Return `true` for valid access keys (16–128 chars, valid char set).
        *   Throw `IamError` with `ValidationError.code` for invalid types, <16 chars, >128 chars, or invalid chars.
    *   `validate_status(status) -> undefined | true`
        *   Return `undefined` if `status` is `undefined`.
        *   Return `true` only for 'Active' or 'Inactive'.
        *   Throw `IamError` with `ValidationError.code` for any other value.

*   Export `IAM_DEFAULT_PATH` from `src/endpoint/iam/iam_utils.js`.

*   Update handlers to validate inputs in their respective files:
    *   `iam_create_user.js`
        *   Validate `req.body.user_name` (required, error for missing/empty).
        *   Validate `req.body.path` (error if no leading/trailing '/').
    *   `iam_get_user.js`
        *   Validate `req.body.user_name` (error for reserved/invalid names).
    *   `iam_update_user.js`
        *   Validate `req.body.user_name` (required, error for missing/empty).
        *   Validate `req.body.new_user_name` (error if empty/exceeds 64 chars).
        *   Validate `req.body.new_path` (error if no leading/trailing '/').
    *   `iam_delete_user.js`
        *   Validate `req.body.user_name` (required, error for missing).
    *   `iam_list_users.js`
        *   Validate `req.body.path_prefix` (error if no leading '/').
        *   Validate `req.body.max_items` (error if 0 or below 1).
        *   Validate `req.body.marker` (error if empty).
    *   `iam_create_access_key.js`
        *   Validate `req.body.user_name` (error if empty).
    *   `iam_get_access_key_last_used.js`
        *   Validate `req.body.access_key_id` (required, error for missing/too short).
    *   `iam_update_access_key.js`
        *   Validate `req.body.access_key_id` (required, error for missing/too short).
        *   Validate `req.body.status` (required, error for missing/invalid).
        *   Validate `req.body.user_name` (error if empty).
    *   `iam_delete_access_key.js`
        *   Validate `req.body.access_key_id` (required, error for missing/too short).
        *   Validate `req.body.user_name` (error if empty).
    *   `iam_list_access_keys.js`
        *   Validate `req.body.max_items` (error if 0 or below 1).
        *   Validate `req.body.marker` (error if empty).

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.