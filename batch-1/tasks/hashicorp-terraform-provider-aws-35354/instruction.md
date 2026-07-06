Implement a new Terraform resource to manage AWS Security Lake custom log sources, allowing users to specify source details and configurations. Ensure the resource supports full lifecycle management, including import functionality, and update test helpers for consistency.

*   Implement a new Terraform resource type `aws_securitylake_custom_log_source` in the `internal/service/securitylake` package using the function `newCustomLogSourceResource`.
    *   Support top-level attributes: `source_name` (string, serves as the resource ID), `source_version` (string), and `event_classes` (set of strings).
    *   Include a configuration block with:
        *   `crawler_configuration` sub-block containing `role_arn` (string).
        *   `provider_identity` sub-block containing `external_id` and `principal` (strings).
*   Support Terraform import by `source_name`.
    *   Ensure import state verification succeeds while ignoring `configuration` and `event_classes` attributes.
*   Implement `findCustomLogSourceBySourceName` function with the signature:
    ```go
    findCustomLogSourceBySourceName(ctx context.Context, conn *securitylake.Client, sourceName string) (*types.CustomLogSourceResource, error)
    ```
    *   Return a `tfresource.NotFound`-compatible error if no resource with the given source name exists.
*   Export necessary functions for testing in `exports_test.go`:
    *   `ResourceCustomLogSource = newCustomLogSourceResource`
    *   `FindCustomLogSourceBySourceName = findCustomLogSourceBySourceName`
*   Update test helpers:
    *   In `data_lake_test.go`, convert `testAccDataLakeConfigConfig_base` to a package-level string constant.
    *   Remove `rName` parameter from `testAccDataLakeConfig_basic()`, `testAccDataLakeConfig_tags1(tag1Key, tag1Value string)`, and `testAccDataLakeConfig_tags2(tag1Key, tag1Value, tag2Key, tag2Value string)`.
    *   In `aws_log_source_test.go`, remove `rName` parameter from `testAccAWSLogSourceConfig_basic()`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.