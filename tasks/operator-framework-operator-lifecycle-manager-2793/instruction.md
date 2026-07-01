Implement security hardening for pods created during operator bundle unpacking and catalog source registry pods. Ensure these pods adhere to strict security requirements by setting appropriate security contexts.

*   Update the bundle unpacker to create pods with the following pod-level security context:
    *   Set `RunAsNonRoot=true`.
    *   Set `RunAsUser=1001`.
    *   Apply a seccomp profile of type `RuntimeDefault`.

*   Configure every container and init container within the bundle unpacking pods to have the following security context:
    *   Set `Privileged=false`.
    *   Set `ReadOnlyRootFilesystem=false`.
    *   Set `AllowPrivilegeEscalation=false`.
    *   Configure `Capabilities` to drop all capabilities.

*   Modify the registry reconciler to ensure that catalog source registry pods have the following container security context:
    *   Include `Privileged=false` in addition to existing security fields like `ReadOnlyRootFilesystem`, `AllowPrivilegeEscalation`, and `Capabilities`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.