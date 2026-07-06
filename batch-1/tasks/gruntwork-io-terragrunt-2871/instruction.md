Implement the `ListStackDependentModules` function in `configstack/module.go` to generate a reverse-dependency map for a given stack of modules. This map should identify which modules depend on each module, directly and transitively, while handling circular dependencies gracefully.

*   Implement `ListStackDependentModules(stack *Stack) map[string][]string` to:
    *   Accept a pointer to a `Stack` as input.
    *   Return a map where:
        *   Each key is the `Path` of a module with at least one dependent.
        *   Each value is an ordered list of module paths that depend on the key module.
            *   Direct dependents must precede transitive dependents.
            *   Ensure no duplicate entries in the list.
*   Ensure modules with no dependents are not included as keys in the map.
*   Handle circular dependencies without causing infinite loops or panics.
    *   For circular dependencies, ensure each module in the cycle is listed as a dependent of the others.
*   Maintain the correct order of dependents:
    *   For a dependency chain A→B→C, the entry for C should be ["B", "A"].
    *   For deeper chains like A→B→C→D, the entry for D should be ["C", "B", "A"].

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.