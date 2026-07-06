Implement a token-based promotion mechanism for PostgreSQL replica clusters to ensure safe and verifiable cluster switchovers. When a cluster is demoted, generate a token capturing its last checkpoint state. Validate and verify this token during replica promotion requests to prevent premature promotions.

*   Implement the `ShouldPromoteFromReplicaCluster()` method in `api/v1/cluster_types.go`:
    *   Return `false` if `ReplicaCluster` is `nil`, `PromotionToken` is empty, or matches `Status.LastPromotionToken`.
    *   Return `true` if `PromotionToken` is non-empty and differs from `Status.LastPromotionToken`.

*   Implement the `validatePromotionToken()` method in `api/v1/cluster_webhook.go`:
    *   Return validation errors if `PromotionToken` is not valid base64 or lacks required fields.
    *   Return no errors if `ReplicaCluster` is `nil`, `PromotionToken` is empty, or the token is fully valid.

*   Update `ClusterStatus` in `api/v1/cluster_types.go`:
    *   Add `LastPromotionToken` (JSON key: `lastPromotionToken`) to store the last applied promotion token.
    *   Add `DemotionToken` (JSON key: `demotionToken`) to store the token generated during demotion.

*   Update `ReplicaClusterConfiguration` in `api/v1/cluster_types.go`:
    *   Add `PromotionToken` (JSON key: `promotionToken`) for operators to request replica promotion.

*   Enhance `pkg/utils` package:
    *   Create `PgControldataTokenContent` struct with fields: `LatestCheckpointTimelineID`, `REDOWALFile`, `DatabaseSystemIdentifier`, `LatestCheckpointREDOLocation`, `TimeOfLatestCheckpoint`, `OperatorVersion`.
    *   Implement `IsValid()` to check for empty fields and `Encode()` to serialize as base64-encoded JSON.
    *   Implement `PgDataState` type with `IsShutdown(ctx context.Context) bool` method.
    *   Implement `ParsePgControldataOutput(output string)` to parse pg_controldata output into a key-value map.
    *   Implement `CreatePromotionToken(parsedControlData map[string]string)` to generate a base64-encoded token.
    *   Implement `ParsePgControldataToken(token string)` to decode base64 tokens into `PgControldataTokenContent`.

*   Enhance `pkg/promotiontoken` package:
    *   Create `TokenVerificationError` type with `IsRetryable() bool`.
    *   Implement `ValidateAgainstLSN(token *utils.PgControldataTokenContent, currentLSN string) error`.
    *   Implement `ValidateAgainstTimelineID(token *utils.PgControldataTokenContent, currentTimelineID string) error`.
    *   Implement `ValidateAgainstSystemIdentifier(token *utils.PgControldataTokenContent, currentSystemID string) error`.
    *   Implement `ValidateAgainstInstanceStatus(token *utils.PgControldataTokenContent, systemID string, timelineID string, lsn string) error`.

*   Update the cluster reconciler struct in `internal/controller`:
    *   Rename `StatusClient` to `InstanceClient` and ensure all initializations reflect this change.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.