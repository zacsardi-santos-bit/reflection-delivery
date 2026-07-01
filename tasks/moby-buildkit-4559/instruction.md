Implement a mechanism in the build scheduler to detect and prevent cycles when merging edges between build graph vertices with the same cache key. Ensure that the scheduler completes builds successfully without errors, even in complex graph configurations.

*   Detect potential cycles:
    *   Identify when merging edges between two vertices with the same cache key would create a cycle.
    *   Avoid creating cycles by choosing a safe merge direction instead of proceeding blindly.

*   Handle multiple-owner scenarios:
    *   Recognize when a vertex has been merged multiple times through different ownership chains.
    *   Prevent cycles by determining the safe merge direction when a further merge through one of those owners would introduce a cycle.

*   Ensure correct computed results:
    *   For a graph with two vertices each adding a constant to two inputs (values 3 and 4, constant 2) combined via a summation vertex, produce a result of 23.
    *   For a graph with three vertices each adding to three inputs (values 3, 4, and 5, constant 2) in the multiple-owner configuration, produce a result of 37.

*   Maintain deterministic behavior:
    *   Ensure the cycle-avoidance logic is consistent and not influenced by timing or ordering variations between runs.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.