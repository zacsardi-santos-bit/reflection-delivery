# Add Data Catalog Safety Checks to R2 Write Operations

## Description

R2 buckets can have a "data catalog" feature enabled, which maintains metadata about the bucket's contents. When users perform write operations — such as uploading or deleting objects, running bulk uploads, or modifying lifecycle rules — these operations can potentially leave the data catalog in an inconsistent state if done without awareness.

Currently, the CLI has no way to warn users about this risk, and there is no mechanism to let users make an informed decision before proceeding with operations that could corrupt catalog metadata.

## Expected Behavior

- When performing object upload, object deletion, lifecycle rule addition, or lifecycle rule replacement operations, the CLI should first signal to the API that it wants to check whether the bucket's data catalog would be affected.
- If the API indicates a conflict (the bucket has data catalog enabled), the user should be prompted to confirm whether they want to proceed, knowing the catalog may end up in an invalid state.
- If the user confirms, the operation should be retried and completed.
- If the user declines, the operation should be cancelled with a clear message.
- For bulk uploads, the warning should appear upfront before any work begins, since individual per-object checks are not practical at scale.
- Users who already know they want to bypass the check — for example, in automated pipelines — should be able to pass a flag to skip the prompt entirely.
- In non-interactive environments such as CI, the operation should proceed automatically without requiring human input.
- Lifecycle rule removal operations should not trigger any catalog check, since removing rules cannot corrupt the catalog.

## Why This Matters

Without this protection, users running operations on buckets with data catalog enabled could unknowingly invalidate their catalog metadata, leading to data inconsistencies that are difficult to detect and recover from. This change makes the CLI a safer tool for users working with data catalog-enabled buckets.
