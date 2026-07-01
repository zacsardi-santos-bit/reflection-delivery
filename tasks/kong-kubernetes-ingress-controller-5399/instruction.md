Implement protocol annotation validation and error handling in the Kong Kubernetes Ingress Controller to support WebSocket protocols and provide immediate feedback on misconfigurations. Update validation logic to recognize 'ws' and 'wss' as valid protocols and ensure invalid protocol annotations are caught at submission time.

*   Update the `ValidateProtocol` function in `internal/util/protocol.go`:
    *   Ensure it returns true for an empty string, "ws", and "wss", in addition to existing valid protocols.

*   Modify the `ValidateIngress` function in `internal/admission/validation/ingress/ingress.go`:
    *   Validate the `konghq.com/protocols` annotation before other checks.
    *   Return `(false, "Ingress has invalid Kong annotations: invalid konghq.com/protocols value: <value>", nil)` if the annotation contains an invalid protocol.

*   Adjust the `ValidateHTTPRoute` function in `internal/admission/validation/gateway/httproute.go`:
    *   Check the `konghq.com/protocols` annotation.
    *   Return `(false, "HTTPRoute has invalid Kong annotations: invalid konghq.com/protocols value: <value>", nil)` if the annotation contains an invalid protocol.

*   Revise the `override` method in `internal/dataplane/kongstate/service.go`:
    *   Change the return type to `error`.
    *   Return `nil` if the receiver is nil or all protocol annotations are valid.
    *   Return an error with the message "konghq.com/protocol annotation has invalid value: <value>" if an invalid protocol is found.

*   Update the `FillOverrides` method in `internal/dataplane/kongstate/kongstate.go`:
    *   Handle the error return from `override()`.
    *   Record a resource failure with the message "konghq.com/protocol annotation has invalid value: <value>" if `override()` returns an error.

*   Ensure protocol annotation validation occurs before other validations in both `ValidateIngress` and `ValidateHTTPRoute` functions to prevent further processing of invalid resources.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.