Implement access to the `scope` and `policy version` fields for both the principal and resource in Cerbos policy condition expressions. Ensure these fields are available for writing fine-grained, context-aware authorization rules.

*   Update the `Request_Principal` struct in `api/genpb/cerbos/engine/v1/engine.pb.go`:
    *   Add `PolicyVersion` and `Scope` string fields.
    *   Implement `GetPolicyVersion() string` and `GetScope() string` methods.
*   Update the `Request_Resource` struct in `api/genpb/cerbos/engine/v1/engine.pb.go`:
    *   Add `PolicyVersion` and `Scope` string fields.
    *   Implement `GetPolicyVersion() string` and `GetScope() string` methods.
*   Modify the `checkInputToRequest` function in `internal/engine/evaluator.go`:
    *   Copy `PolicyVersion` and `Scope` from `input.Principal` to the request principal.
    *   Copy `PolicyVersion` and `Scope` from `input.Resource` to the request resource.
*   Update the proto definition in `api/public/cerbos/engine/v1/engine.proto`:
    *   Declare `string policy_version = 4;` and `string scope = 5;` for both `Request.Principal` and `Request.Resource`.
*   Ensure policy condition expressions can access:
    *   `request.principal.scope` and `request.principal.policyVersion`.
    *   `request.resource.scope` and `request.resource.policyVersion`.
*   Validate that a policy condition checking `request.principal.scope == request.resource.scope` AND `request.principal.policyVersion == request.resource.policyVersion` returns EFFECT_ALLOW when scopes and policy versions match.
*   Update the JSON schema for CEL test cases in `internal/test/testdata/.jsonschema/CelTestCase.schema.json`:
    *   Accept `policyVersion` (string) and `scope` (string) as valid fields on both the principal and resource objects.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.