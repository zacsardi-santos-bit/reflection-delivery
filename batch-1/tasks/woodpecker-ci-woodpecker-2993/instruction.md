Implement protocol support for port definitions in pipeline service configurations. Allow users to specify ports with an optional protocol using the format "NUMBER" or "NUMBER/protocol" in YAML configurations. Ensure the protocol is correctly propagated through backend systems like Docker and Kubernetes.

*   Define a new `Port` struct in `pipeline/backend/types/network.go`:
    *   Fields: `Number` (uint16) and `Protocol` (string).
    *   Use JSON serialization with `omitempty` for both fields.

*   Update the `Step` struct in `pipeline/backend/types/step.go`:
    *   Change the `Ports` field from `[]uint16` to `[]Port`.

*   Update the `Container` struct in `pipeline/frontend/yaml/types/container.go`:
    *   Change the `Ports` field from `[]base.StringOrInt` to `[]string`.

*   Implement the `convertPort` function in `pipeline/frontend/yaml/compiler/convert.go`:
    *   Accept a port definition string in "NUMBER" or "NUMBER/protocol" format.
    *   Return a `Port` with `Number` set to the parsed value and `Protocol` set to the protocol portion.
    *   Return an error for invalid formats:
        *   Protocol before port number (e.g., 'tcp/1234').
        *   Invalid delimiter (e.g., '1234|udp').
        *   Non-numeric port portion (e.g., 'http').

*   Ensure Kubernetes service and pod container ports:
    *   Convert the port protocol to uppercase (e.g., 'tcp' to 'TCP').
    *   Omit the protocol field if no protocol is specified.

*   Ensure Docker backend step configurations use the `Ports` field as `[]Port` instead of `[]uint16`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.