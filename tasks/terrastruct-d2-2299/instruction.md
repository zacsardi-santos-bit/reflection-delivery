Implement two new boolean filters, `&leaf` and `&connected`, in D2's glob pattern system to enhance node styling capabilities based on structural position and topological connectivity.

*   Update the `ampersandFilter` function in `d2ir/compile.go` to support:
    *   A boolean `&leaf` filter:
        *   When `&leaf: true`, apply attributes only to leaf nodes (nodes with no children).
        *   When `&leaf: false`, apply attributes only to container nodes (nodes with children).
        *   Ensure nodes that do not match the filter have their attributes remain unset (nil).
    *   A boolean `&connected` filter:
        *   When `&connected: true`, apply attributes only to nodes participating in at least one edge.
        *   When `&connected: false`, apply attributes only to isolated nodes (nodes with no edges).
        *   Ensure nodes that do not match the filter have their attributes remain unset (nil).

*   Ensure both `&leaf` and `&connected` filters accept boolean values `true` or `false`.
*   Modify the switch statement in `ampersandFilter` to handle new cases for `"leaf"` and `"connected"` alongside existing filters like `label` and `exists`.
*   Verify that:
    *   For a hierarchy `a.b.c`, applying a glob with `&leaf: false` and `style.fill: red` results in `a` and `b` having `Attributes.Style.Fill.Value` as `"red"`, while `c` has `Attributes.Style.Fill` as nil.
    *   For a diagram with nodes `a -> b` and `c`, applying a glob with `&connected: true` and `style.fill: red` results in `a` and `b` having `Attributes.Style.Fill.Value` as `"red"`, while `c` has `Attributes.Style.Fill` as nil.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.