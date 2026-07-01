Implement a new lint check for StatefulSet resources that ensures each volume claim template includes a required annotation. Develop a configurable linting template to enforce this policy and produce diagnostics for non-compliance.

*   Implement the `StatefulSetSpec` function in `pkg/extract/sts_spec.go`.
    *   Accept a `k8sutil.Object` and return the StatefulSet spec and a boolean.
    *   Return an empty spec and false for nil input or non-StatefulSet objects.
    *   Use reflection to extract the spec for objects embedding a StatefulSet spec with Kind 'StatefulSet'.

*   Implement the `GetPersistentVolumeClaimAPIVersion` function in `pkg/objectkinds/pvc.go`.
    *   Return a non-empty string representing the API version for PersistentVolumeClaim resources.

*   Define the `Params` struct in `pkg/templates/volumeclaimtemplates/internal/params/params.go`.
    *   Include an `Annotation` string field.
    *   Implement a `Validate()` method returning nil if `Annotation` is non-empty, or an error with the message 'invalid parameters: required param annotation not found' if empty.

*   Implement the `ParseAndValidate` function in `pkg/templates/volumeclaimtemplates/internal/params/gen-params.go`.
    *   Accept a `map[string]interface{}` and return a `Params` value and nil error if the map contains a valid 'annotation' key.
    *   Return an error containing 'required param annotation not found' if the key is absent or validation fails.

*   Implement the `WrapInstantiateFunc` function in `pkg/templates/volumeclaimtemplates/internal/params/gen-params.go`.
    *   Wrap a function taking `Params` into a generic function accepting `interface{}`.
    *   Ensure the wrapped function casts the argument to `Params` and delegates to the original function.

*   Register the linting template `statefulset-volumeclaimtemplate-annotation` in `pkg/templates/volumeclaimtemplates/template.go`.
    *   Ensure `templates.Get('statefulset-volumeclaimtemplate-annotation')` returns a non-nil template.
    *   When instantiated with `Params` specifying a required annotation key:
        *   Return zero diagnostics if all VolumeClaimTemplates carry the annotation.
        *   Return one diagnostic per missing annotation with the message: "StatefulSet's VolumeClaimTemplate is missing required annotation: <annotation-name>".

*   Implement the `AddObject` method for `MockLintContext` in `pkg/lintcontext/mocks/context.go`.
    *   Store the object under the given key for retrieval by the mock context's `Objects()` method.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.