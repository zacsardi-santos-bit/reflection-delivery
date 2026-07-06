Implement end-to-end test coverage for embedded cluster installation scenarios, including both airgapped and online environments. Update the shared dashboard graph validation helper to accommodate embedded clusters, ensuring it functions correctly across all cluster types.

*   Update the `validateDashboardGraphs` function in `e2e/playwright/regression/shared/dashboard.ts`:
    *   Accept a third boolean parameter `isExistingCluster` in addition to `page` and `expect`.
    *   If `isExistingCluster` is true, configure the Prometheus metrics endpoint by filling in the URL and saving before checking for graph content.
    *   If `isExistingCluster` is false, skip the Prometheus endpoint configuration step and proceed directly to checking for graph content.
    *   Ensure the graphs card contains 'Disk Usage', 'CPU Usage', and 'Memory Usage' text in both scenarios.

*   Create a constants file at `e2e/playwright/regression/@embedded-airgapped-install/constants.ts`:
    *   Export the following named constants:
        *   `NAMESPACE`: string
        *   `IS_EXISTING_CLUSTER`: boolean (false)
        *   `IS_AIRGAPPED`: boolean (true)
        *   `IS_EC`: boolean
        *   `IS_AIRGAP_SUPPORTED`: boolean
        *   `IS_MINIMAL_RBAC`: boolean
        *   `CUSTOMER_ID`: string
        *   `CUSTOMER_NAME`: string
        *   `CHANNEL_ID`: string
        *   `CHANNEL_NAME`: string
        *   `CHANNEL_SLUG`: string
        *   `LICENSE_ID`: string
        *   `INITIAL_SMALL_BUNDLE_CHANNEL_SEQUENCE`: number
        *   `UPDATE_SMALL_BUNDLE_CHANNEL_SEQUENCE`: number
        *   `VENDOR_INITIAL_CHANNEL_SEQUENCE`: number
        *   `VENDOR_UPDATE_CHANNEL_SEQUENCE`: number
        *   `DOWNLOAD_PORTAL_BASE64_PASSWORD`: string

*   Create a constants file at `e2e/playwright/regression/@embedded-online-install/constants.ts`:
    *   Export the following named constants:
        *   `NAMESPACE`: string
        *   `IS_EXISTING_CLUSTER`: boolean (false)
        *   `IS_AIRGAPPED`: boolean (false)
        *   `IS_EC`: boolean
        *   `IS_AIRGAP_SUPPORTED`: boolean
        *   `IS_MINIMAL_RBAC`: boolean
        *   `CUSTOMER_ID`: string
        *   `CUSTOMER_NAME`: string
        *   `CHANNEL_ID`: string
        *   `CHANNEL_NAME`: string
        *   `LICENSE_ID`: string
        *   `VENDOR_INITIAL_CHANNEL_SEQUENCE`: number
        *   `VENDOR_UPDATE_CHANNEL_SEQUENCE`: number

*   Ensure all existing `@existing-*` test specs pass `constants.IS_EXISTING_CLUSTER` (true) as the third argument to `validateDashboardGraphs`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.