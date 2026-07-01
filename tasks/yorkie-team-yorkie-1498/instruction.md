Implement per-project webhook configuration to allow independent settings for retry counts, wait intervals, and request timeouts. Update the webhook client to apply these settings dynamically at send time, and modify the data model to support these changes.

*   Update the webhook client:
    *   Ensure the `NewClient` function in `pkg/webhook/client.go` takes no arguments.
    *   Modify the `Send` method in `pkg/webhook/client.go` to accept an `Options` value as the final argument.

*   Modify project update capabilities:
    *   Add new optional fields to `UpdatableProjectFields` in `api/types/updatable_project_fields.go` for both auth and event webhooks:
        *   `AuthWebhookMaxRetries`, `AuthWebhookMinWaitInterval`, `AuthWebhookMaxWaitInterval`, `AuthWebhookRequestTimeout`
        *   `EventWebhookMaxRetries`, `EventWebhookMinWaitInterval`, `EventWebhookMaxWaitInterval`, `EventWebhookRequestTimeout`
    *   Ensure `UpdatableProjectFields.Validate()` returns a form validation error for invalid Go duration strings in the new fields and `ClientDeactivateThreshold`.

*   Update the project data model:
    *   Add new fields to the `Project` struct in `api/types/project.go` for both auth and event webhooks:
        *   `AuthWebhookMaxRetries`, `AuthWebhookMinWaitInterval`, `AuthWebhookMaxWaitInterval`, `AuthWebhookRequestTimeout`
        *   `EventWebhookMaxRetries`, `EventWebhookMinWaitInterval`, `EventWebhookMaxWaitInterval`, `EventWebhookRequestTimeout`
    *   Implement `GetAuthWebhookOptions` and `GetEventWebhookOptions` methods in `api/types/project.go` to parse timing fields into `webhook.Options` and return errors for invalid durations.

*   Adjust project creation and update processes:
    *   Remove `clientDeactivateThreshold` from `NewProjectInfo` in `server/backend/database/project_info.go`.
    *   Remove `clientDeactivateThreshold` from `CreateProjectInfo` in `server/backend/database/database.go`.
    *   Add new fields to `ProjectInfo` in `server/backend/database/project_info.go` for both auth and event webhooks:
        *   `AuthWebhookMaxRetries`, `AuthWebhookMinWaitInterval`, `AuthWebhookMaxWaitInterval`, `AuthWebhookRequestTimeout`
        *   `EventWebhookMaxRetries`, `EventWebhookMinWaitInterval`, `EventWebhookMaxWaitInterval`, `EventWebhookRequestTimeout`
    *   Update `ProjectInfo.UpdateFields()` to apply new webhook fields from `UpdatableProjectFields`.

*   Simplify global server configuration:
    *   Remove the following fields from `Config` in `server/backend/config.go`:
        *   `ClientDeactivateThreshold`, `AuthWebhookMaxWaitInterval`, `AuthWebhookMinWaitInterval`, `AuthWebhookRequestTimeout`, `AuthWebhookMaxRetries`
        *   `EventWebhookMaxWaitInterval`, `EventWebhookMinWaitInterval`, `EventWebhookRequestTimeout`, `EventWebhookMaxRetries`
    *   Ensure `Config.Validate()` only validates remaining fields: `AdminTokenDuration`, `AuthWebhookCacheTTL`, `ProjectCacheTTL`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.