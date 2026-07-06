Implement a new CLI subcommand to display CPU and memory usage for Crossplane-related pods in a specified namespace. Ensure the output is a formatted table with pod details and includes an optional summary mode for aggregated results. Classify pods based on their labels and provide a helper function to capitalize the first character of a string.

*   Implement the `getCrossplanePods` function:
    *   Accept a slice of Kubernetes pod objects and return a `[]topMetrics` slice.
    *   Recognize a pod as Crossplane-related if it has a label with a key starting with 'pkg.crossplane.io/' or 'app.kubernetes.io/part-of' with value 'crossplane'.
    *   Set `PodType` to the portion of the label key after '/' or to 'crossplane' for the core controller.
    *   Populate `PodName` and `PodNamespace` with the pod's name and namespace.

*   Define the `topMetrics` struct in `cmd/crank/beta/top/top.go`:
    *   Include fields: `PodType` (string), `PodName` (string), `PodNamespace` (string), `CPUUsage` (resource.Quantity), and `MemoryUsage` (resource.Quantity).

*   Implement the `printPodsTable` function:
    *   Write a tab-aligned table to the provided writer with columns: TYPE, NAMESPACE, NAME, CPU(cores), MEMORY.
    *   Format CPU as '<millivalue>m' and memory as '<integer>Mi'.
    *   Return `nil` on success.

*   Implement the `printPodsSummary` function:
    *   Write to the provided writer: total pod count, per-type counts, total memory in MiB, and total CPU in millicores.
    *   Format pod type names using `capitalizeFirst` and list them alphabetically.

*   Implement the `capitalizeFirst` function:
    *   Return an empty string for empty input.
    *   Return the input unchanged if the first character is not a letter.
    *   Otherwise, return the string with only its first character uppercased.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.