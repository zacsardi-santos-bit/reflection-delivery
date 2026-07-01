Simplify the handling of project identifiers in the webhook proxy service by centralizing the project context at the server level. Remove the requirement for callers to include a project name in every build trigger request payload and update internal structures accordingly.

*   Update the `Server` struct in `jenkins/webhook-proxy/main.go`:
    *   Add a `Project` field of type `string`.
    *   Ensure `Namespace` and `Project` fields are settable independently during `Server` instance construction.
    *   Derive the `Project` value by removing the "-cd" suffix from the `Namespace` string at server initialization.

*   Modify the `Event` struct in `jenkins/webhook-proxy/main.go`:
    *   Remove the `Project` field.
    *   Ensure valid fields include `Kind`, `Namespace`, `Repo`, `Component`, `Branch`, `Pipeline`, and `Env`.

*   Adjust the `IsValid` method for the `Event` struct:
    *   Signature: `(e *Event) IsValid() bool`.
    *   Return `true` if `Pipeline` is at least 3 characters long and `Namespace`, `Repo`, `Component`, and `Branch` fields are non-empty.
    *   Do not check for a `Project` field.

*   Update the build trigger request payload structure:
    *   Located in `jenkins/webhook-proxy/main.go` inside `HandleRoot`.
    *   Use the `requestBuild` struct without a `Project` field.
    *   Include only `Branch`, `Repository`, and `Env` fields.

*   Ensure the server uses its `Project` field for processing:
    *   Use the server-level `Project` to derive component and pipeline names.
    *   Construct pipeline names using the server's `Project`, the component name, and the branch name.

*   Use the `namespaceSuffix` constant:
    *   Located in `jenkins/webhook-proxy/main.go`.
    *   Value: "-cd".
    *   Apply `strings.TrimSuffix` to derive the project name from the namespace.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.