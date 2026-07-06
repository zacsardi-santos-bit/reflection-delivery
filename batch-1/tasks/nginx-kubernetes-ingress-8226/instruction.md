Implement the specified changes to the NGINX Kubernetes Ingress controller to ensure that proxy buffer settings are handled correctly based on the directive auto-adjust feature. Improve error messaging for invalid size-type annotations to provide clearer guidance to users.

*   Update the `ConfigParams` struct in `internal/configs/config_params.go`:
    *   Change `ProxyBuffers`, `ProxyBufferSize`, and `ProxyBusyBuffersSize` fields to plain string types.

*   Modify `ParseConfigMap` function in `internal/configs/configmaps.go`:
    *   Add a `directiveAutoadjustEnabled` boolean parameter.
    *   When `directiveAutoadjustEnabled` is true, validate and normalize proxy buffer settings; set `configOk` to false and generate a warning event for invalid formats.
    *   When `directiveAutoadjustEnabled` is false, store proxy buffer values as plain strings without validation or normalization, and ensure `configOk` is always true for proxy buffer settings.

*   Update `ParseProxyBuffersSpec` function in `internal/configs/parsing_helpers.go`:
    *   Merge previous auto-adjust variant functionality.
    *   Normalize valid units (e.g., uppercase K to lowercase k, g/G to m).
    *   Return errors for invalid inputs such as empty strings, non-numeric values, count-only, size-only, three-part values, and unrecognized unit letters.

*   Revise `NewSizeWithUnit` function in `internal/validation/data_types.go`:
    *   Change signature to `NewSizeWithUnit(sizeStr string, autoAdjust bool) (string, error)`.
    *   Return a plain string instead of a struct, with auto-adjustment controlled by the second parameter.

*   Implement `newNumberSizeConfig` function in `internal/validation/data_types.go`:
    *   Signature: `newNumberSizeConfig(sizeStr string, autoAdjust bool) (string, error)`.
    *   Replace previous `NewNumberSizeConfig` function.
    *   Handle normalization and replacement of invalid units, returning a plain string.

*   Update `BalanceProxyValues` function in `internal/validation/data_types.go`:
    *   Signature: `BalanceProxyValues(proxyBuffers, proxyBufferSize, proxyBusyBuffersSize string, autoAdjust bool) (string, string, string, []string, error)`.
    *   Accept and return plain strings for all proxy buffer parameters.

*   Modify `BalanceProxiesForUpstreams` function in `internal/validation/data_types.go`:
    *   Preserve invalid proxy buffer size values as-is when `autoadjust` is false, rather than replacing them with defaults.

*   Improve error message for invalid size-type annotations:
    *   Change message to: "must consist of numeric characters followed by a valid size suffix. 'k|K|m|M (e.g. '16', or '32k', or '64M', regex used for validation is '\d+[kKmM]?')".

*   Remove `ParseSizeWithAutoAdjust` function from `internal/configs/parsing_helpers.go`.

*   Remove `ParseSize` and `FormatSize` functions from the `internal/validation` package.

*   Move `data_types_test.go` from `internal/validation` to the `internal` package, adjusting the package declaration accordingly.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.