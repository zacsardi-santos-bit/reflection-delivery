Fix the user management system by addressing issues with profile image retrieval, user settings, and password updates. Implement new functions to improve user status management and correct existing functionality to ensure proper data handling.

*   Update the `Update` function in `master/internal/user/postgres_users.go`:
    *   Accept a pointer to `AgentUserGroup` as the fourth parameter, allowing it to be nil.
    *   Skip agent user group operations if the `AgentUserGroup` pointer is nil.
    *   Support updating the `password_hash` field via the `toUpdate` slice without requiring a non-nil `AgentUserGroup`.

*   Implement the `SetActive` function in `master/internal/user/postgres_users.go`:
    *   Signature: `SetActive(ctx context.Context, userIDs []model.UserID, status bool) error`.
    *   Set the `Active` field for all specified users to the given status.
    *   Ensure calling with an empty `userIDs` slice succeeds without error or modification.

*   Implement the `ProfileImage` function in `master/internal/user/postgres_users.go`:
    *   Signature: `ProfileImage(ctx context.Context, username string) ([]byte, error)`.
    *   Return profile image bytes for the user identified by `username`.
    *   Join `user_profile_images` on the `user_id` column.

*   Define the `UserProfileImage` struct in `master/internal/user/postgres_users.go`:
    *   Fields: `ID int`, `UserID model.UserID`, `FileData []byte`.
    *   Backed by the `user_profile_images` database table.

*   Implement the `ResetUserSetting` function in `master/internal/user/postgres_users.go`:
    *   Signature: `ResetUserSetting(ctx context.Context, userID model.UserID) error`.
    *   Delete all web settings for the specified user.
    *   Ensure it is a no-op if no settings exist.

*   Implement the `UpdateUserSetting` function in `master/internal/user/postgres_users.go`:
    *   Signature: `UpdateUserSetting(ctx context.Context, settings []*model.UserWebSetting) error`.
    *   Upsert each setting, deleting any with an empty `Value`.

*   Implement the `GetUserSetting` function in `master/internal/user/postgres_users.go`:
    *   Signature: `GetUserSetting(ctx context.Context, userID model.UserID) ([]*model.UserWebSetting, error)`.
    *   Return all stored (non-deleted) settings for the specified user.

*   Update the `UserWebSetting` struct in `master/pkg/model/user.go`:
    *   Fields: `UserID UserID`, `Key string`, `Value string`, `StoragePath string`.
    *   Implement a `Proto` method to convert to a protobuf representation.

*   Add a `PasswordHash` field to the `User` struct in `master/pkg/model/user.go`:
    *   Type: `null.String` from `gopkg.in/guregu/null.v3`.
    *   Allow updating via the `Update` function with `toUpdate` containing `"password_hash"`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.