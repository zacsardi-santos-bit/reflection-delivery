Implement an alternative hostname resolution mode in the Datadog exporter for OpenTelemetry Collector. Modify the hostname resolution functions for AWS EC2, Azure, and GCP to accept a boolean flag that determines whether to use this new mode, aligning with Datadog's native cloud integration conventions.

*   Update the `HostnameFromAttributes` function in the Azure package:
    *   Accept a `usePreviewRules` boolean parameter.
    *   Return the VM/host identifier when `usePreviewRules` is true.
    *   Return the host name attribute when `usePreviewRules` is false.
    *   Return `("", false)` if the relevant attribute is absent.

*   Update the `HostInfoFromAttributes` function in the Azure package:
    *   Accept a `usePreviewRules` boolean parameter.
    *   Set `HostAliases` to empty when `usePreviewRules` is true.
    *   Include the VM/host identifier in `HostAliases` when `usePreviewRules` is false.
    *   Ensure the function never returns nil.

*   Update the `HostnameFromAttributes` function in the EC2 package:
    *   Accept a `usePreviewRules` boolean parameter.
    *   Return the instance identifier directly when `usePreviewRules` is true.
    *   Use existing behavior when `usePreviewRules` is false.

*   Update the `HostnameFromAttributes` function in the GCP package:
    *   Accept a `usePreviewRules` boolean parameter.
    *   Return the GCP integration hostname in the format `shortname.cloudAccountID` when `usePreviewRules` is true.
    *   Return the raw host name attribute when `usePreviewRules` is false.
    *   Return `("", false)` if required attributes are absent in preview mode.

*   Update the `HostInfoFromAttributes` function in the GCP package:
    *   Accept a `usePreviewRules` boolean parameter.
    *   Populate `GCPTags` in both modes.
    *   Set `HostAliases` to empty when `usePreviewRules` is true.
    *   Include the GCP integration hostname in `HostAliases` when `usePreviewRules` is false.
    *   Ensure the function never returns nil.

*   Update the `HostnameFromAttributes` function in the top-level attributes package:
    *   Accept a `usePreviewRules` boolean parameter.
    *   Exclude container ID from hostname resolution when `usePreviewRules` is true.
    *   Forward the `usePreviewRules` flag to all cloud-provider-specific hostname resolution functions.
    *   Reject hostnames resolving to localhost/loopback addresses, returning `("", false)`.

*   Ensure existing behavior is preserved when `usePreviewRules` is false:
    *   For AWS EC2, return the host name attribute if non-default, otherwise the instance ID.
    *   For Azure, return the host name attribute.
    *   For GCP, return the raw host name.
    *   Maintain custom Datadog hostname attribute priority, Kubernetes node/cluster name formatting, ECS Fargate behavior, and host ID precedence over host name for generic resources.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.