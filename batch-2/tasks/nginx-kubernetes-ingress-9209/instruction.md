I'm working on the NGINX Kubernetes Ingress Controller and I need to add proper support for tracking which Ingress resources reference a given policy.

*   The IsReferencedByIngress method on the policy reference checker must accept a policy namespace, policy name, and an Ingress object, and return true if the Ingress has a policies annotation that lists the given policy — either by bare name (when the ingress namespace matches the policy namespace) or as 'namespace/name'.

*   The IsReferencedByIngress method must handle a comma-separated list of policy entries in the annotation, returning true if any entry matches the given policy namespace and name.

*   The IsReferencedByIngress method must return false when the annotation lists the policy by bare name but the ingress namespace does not match the given policy namespace.

*   The IsReferencedByIngress method must return false when the annotation uses a 'namespace/name' format but the namespace or name does not match the given policy namespace and name.

*   The IsReferencedByMinion method on the policy reference checker must have identical behavior to IsReferencedByIngress — returning true or false under the same conditions.

*   The validateIngressAnnotations function must accept a new IngressOpts struct as its first parameter instead of individual boolean parameters; the struct must have boolean fields: isPlus, appProtectEnabled, appProtectDosEnabled, internalRoutesEnabled, snippetsEnabled, and directiveAutoAdjust.

*   The annotationValidationContext struct must have a field named directiveAutoAdjust (not directiveAutoadjust).

*   A new validatePolicyNames function must accept an *annotationValidationContext (with value and fieldPath fields) and return a field.ErrorList.

*   validatePolicyNames must split the annotation value on commas and validate each trimmed entry individually.

*   validatePolicyNames must support bare policy names (e.g. 'my-policy') and namespaced formats (e.g. 'namespace/policy-name'), treating only the first slash as the namespace separator.

*   validatePolicyNames must return an error with detail containing 'policy name cannot be empty' when a policy entry is empty or whitespace-only after trimming.

*   validatePolicyNames must return an error with detail containing 'policy namespace cannot be empty' when the namespace part of a 'namespace/name' entry is empty or whitespace-only.

*   validatePolicyNames must return an error with detail containing 'policy name must be a valid DNS subdomain' when the policy name fails DNS subdomain validation (e.g. uppercase letters, underscores, leading/trailing hyphens or dots, consecutive dots, or exceeds 253 characters).

*   validatePolicyNames must return an error with detail containing 'policy namespace must be a valid DNS subdomain' when the namespace portion of a namespaced policy entry fails DNS subdomain validation.

*   validatePolicyNames must never panic, even on adversarial inputs such as null bytes, control characters, very long strings, or deeply nested slashes; all returned errors must have non-empty Field and Detail values.


*   Interface details: Type: Constant
Name: PoliciesAnnotation
Location: internal/configs/annotations.go
Signature: const PoliciesAnnotation = "nginx.org/policies"
Description: The annotation key used on Ingress resources to list which policies apply. The value is a comma-separated list of policy names (bare name or namespace/name format).

Type: Method
Name: IsReferencedByIngress
Location: internal/k8s/reference_checkers.go
Signature: (rc *policyReferenceChecker) IsReferencedByIngress(policyNamespace string, policyName string, ing *networking.Ingress) bool
Description: Returns true if the given Ingress has a policies annotation (configs.PoliciesAnnotation = "nginx.org/policies") that references the policy identified by policyNamespace and policyName. Supports bare name format (matches when policyNamespace == ing.Namespace) and "namespace/name" format. Handles comma-separated lists of policy references.

Type: Method
Name: IsReferencedByMinion
Location: internal/k8s/reference_checkers.go
Signature: (rc *policyReferenceChecker) IsReferencedByMinion(policyNamespace string, policyName string, ing *networking.Ingress) bool
Description: Returns the same result as IsReferencedByIngress for the same inputs. Must have identical lookup behavior.

Type: Struct
Name: IngressOpts
Location: internal/k8s/validation.go
Description: Groups all boolean feature flags previously passed as individual parameters to validateIngressAnnotations. Fields: isPlus bool, appProtectEnabled bool, appProtectDosEnabled bool, internalRoutesEnabled bool, snippetsEnabled bool, directiveAutoAdjust bool.

Type: Function
Name: validateIngressAnnotations
Location: internal/k8s/validation.go
Signature: validateIngressAnnotations(ingOpts IngressOpts, annotations map[string]string, specServices map[string]bool, fieldPath *field.Path) field.ErrorList
Description: Validates Ingress annotations using the consolidated IngressOpts struct. Previously accepted isPlus, appProtectEnabled, appProtectDosEnabled, internalRoutesEnabled, snippetsEnabled, and directiveAutoAdjust as individual parameters — these are now grouped into IngressOpts as the first argument.

Type: Function
Name: validatePolicyNames
Location: internal/k8s/validation.go
Signature: validatePolicyNames(context *annotationValidationContext) field.ErrorList
Description: Validates a comma-separated list of policy name entries from an annotation value (context.value). Each entry is trimmed of whitespace. Entries may be bare names or "namespace/name" format (only the first slash separates namespace from name). Returns field.ErrorList with errors whose Detail field contains one of: "policy name cannot be empty", "policy namespace cannot be empty", "policy name must be a valid DNS subdomain", or "policy namespace must be a valid DNS subdomain". Must not panic on any input; all returned errors must have non-empty Field and Detail.

Type: Struct
Name: annotationValidationContext
Location: internal/k8s/validation.go
Description: Context passed to annotation validation functions. Relevant fields: value string (the annotation value), fieldPath *field.Path (the field path for error reporting), directiveAutoAdjust bool (note: the field is named directiveAutoAdjust, not directiveAutoadjust).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.