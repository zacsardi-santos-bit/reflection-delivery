## Description

When authoring component definitions that include custom health checks or status display templates, users encounter two distinct failure modes.

**Panics in health evaluation:** The health status evaluation system crashes with an unhandled panic when a component's health or status template contains certain valid language constructs — specifically, type-constraint declarations (e.g. defining a named schema and using it as a type), private/hidden fields, and pattern-match constraints. Rather than returning a meaningful error, the system crashes entirely. Well-formed templates using these constructs are not supposed to cause panics.

**Overly strict validation with no escape hatch:** The validator that processes individual status sub-fields (such as the details, health policy, or custom status sections) is too restrictive. It only permits a narrow set of expression types and rejects complex dynamic expressions — for example, a loop that iterates over a list of sub-resources (like ingress rules) and generates a distinct status entry for each one. There is no way for a component author to declare "skip validation for this field; I know this expression is valid but complex," so entire categories of useful dynamic status templates are impossible to define.

## Expected Behavior

- Health and status template processing must never panic due to type-constraint declarations, hidden/private fields, or pattern-match constraints in the template.
- A field-level annotation mechanism should allow component authors to mark individual status sub-fields as exempt from structural validation.
- The validation-exempt annotation must survive the full storage lifecycle: encoding → YAML persistence → reload/decode must all succeed without losing the exemption intent, even when the storage layer strips field-level attributes.
- Multiple annotations on the same field must all be individually preserved and restored.
- Re-encoding an already-annotated field must be safe (idempotent — the annotation marker must not be duplicated).
- When a loop-generated status field uses an invalid value type (a nested structure instead of a scalar), the resulting error message must clearly identify the problematic dynamic label.

## Why This Matters

Component authors who write expressive, data-driven status templates — for example, generating one status entry per hostname in an ingress resource — are currently completely blocked. The strict validator rejects their templates, and there is no opt-out path. Separately, templates using otherwise-valid language features like type constraints or hidden fields can trigger crashes rather than returning actionable errors.
