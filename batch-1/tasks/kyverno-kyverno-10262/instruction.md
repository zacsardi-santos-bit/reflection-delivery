Implement the necessary changes to ensure correct accumulation of admission operation types in Kyverno's webhook configuration logic. Address issues with merging operations when multiple rules or policies apply to the same resource kind.

*   Update the `addOpnForValidatingWebhookConf` function:
    *   Accept a slice of rules and an existing map of resource kinds to operation type slices.
    *   Return an updated map where operations are accumulated correctly.
    *   Include all four operations (CREATE, UPDATE, DELETE, CONNECT) for rules specifying no operations.
    *   Ensure that when combined with rules specifying explicit operations, the result contains all operations for that resource kind.
    *   Support incremental accumulation across multiple policies by using the previous call's result as input.

*   Update the `addOpnForMutatingWebhookConf` function:
    *   Accept a slice of rules and an existing map of resource kinds to operation type slices.
    *   Return an updated map where operations are accumulated correctly.
    *   Include only CREATE and UPDATE operations for rules specifying no operations.
    *   Ensure that when combined with rules specifying explicit operations, the result contains the union of explicit operations and the default mutating operations (CREATE, UPDATE).

*   Implement the `mergeOperations` function in `pkg/controllers/webhook/utils.go`:
    *   Accept a map[string]bool (operation type strings as keys, inclusion as values) and a slice of existing `admissionregistrationv1.OperationType` values.
    *   Return a merged slice containing the union of operations from the map and the existing slice.
    *   Ensure behavior matches the previous `getMinimumOperations` function when called with an empty existing operations slice.
    *   Ensure the result is order-independent, focusing on set equality.

*   Modify the internal helper function `appendResource` in `utils.go`:
    *   Replace calls to `getMinimumOperations` with `mergeOperations`, passing the existing operations slice from the map as the second argument.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.