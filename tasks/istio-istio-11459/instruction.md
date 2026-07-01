Implement improvements to the RBAC authorization plugin to support flexible policy matching and simplify its API. Update the metadata list matcher to accept a filter name, simplify TCP and HTTP filter-building functions by removing the proxy version parameter, and add support for policies matching metadata from arbitrary network-layer filters.

*   Modify `buildTCPFilter`:
    *   Accept only two parameters: a `serviceMetadata` pointer and an `rbacOption` value.
    *   Internally set the `forTCPFilter` field in `rbacOption` to `true`.
    *   Ensure the filter configuration uses the struct-based `Config` field.

*   Modify `buildHTTPFilter`:
    *   Accept only two parameters: a `serviceMetadata` pointer and an `rbacOption` value.
    *   Internally set the `forTCPFilter` field in `rbacOption` to `false`.
    *   Ensure the filter configuration uses the struct-based `Config` field.

*   Update `rbacOption` struct:
    *   Include a `forTCPFilter` bool field.

*   Update `generateMetadataListMatcher`:
    *   Accept `filter` string as the first parameter to specify the Envoy filter namespace.

*   Implement `isKeyBinary` in `authz` package (`util.go`):
    *   Return `true` if the key is in the format "prefix[content]".
    *   Return `false` for keys not matching the binary format criteria.

*   Handle constraint keys with `experimental.envoy.filters.` prefix:
    *   Use `isKeyBinary` to identify keys.
    *   Derive filter name by stripping `experimental.` prefix and trailing `[key]`.
    *   Generate metadata matchers:
        *   Use `generateMetadataListMatcher` for list-based matches when constraint values are bracketed.
        *   Use `generateMetadataStringMatcher` for string-based matches when values are plain strings.

*   Convert `request-claims` constraints:
    *   Call `generateMetadataListMatcher` with `authn.AuthnFilterName` as the filter argument.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.