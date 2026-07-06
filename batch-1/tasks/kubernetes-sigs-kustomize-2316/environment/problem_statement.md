## Description

Kustomize is adding a new filter-based architecture for transforming Kubernetes resources as part of a processing pipeline. One common transformation is updating the replica count for a specific named resource. Currently, there is no dedicated filter component in this new architecture to handle replica count updates.

## Expected Behavior

- A new filter component should be available that updates replica count fields in Kubernetes YAML resources.
- The filter should accept a target resource name and a desired replica count, along with a list of field paths where replica counts are defined.
- When applied, the filter should update all specified field paths to the new count for any resource whose metadata name matches the target name.
- If a field path exists in the resource, it should be updated in place.
- If a field path does not exist and the field spec indicates the field should be created, the filter should add the field with the new count.
- If a field path does not exist and creation is not requested, the resource should be left unchanged.
- Resources whose name does not match the target should pass through the filter without modification.
- The filter should support specifying multiple field paths at once, updating all of them in a single pass.

## Why This Matters

Replica count management is a fundamental kustomize operation. Implementing it as a proper pipeline filter enables composable, testable transformations that fit cleanly into the new filter-based architecture alongside other resource transformers.
