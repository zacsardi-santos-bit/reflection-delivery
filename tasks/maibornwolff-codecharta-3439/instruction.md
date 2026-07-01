Implement the necessary changes to ensure that nodes with negative difference values are correctly handled in the CodeCharta visualization tool. Refactor the edge arrow service for better separation of concerns and update CSS class names for clarity.

*   Create a new class `AreaMetricValidPipe` with the method `transform(node: CodeMapNode, areaMetric: string): boolean`.
    *   Return `true` if `node.attributes[metric]` is greater than 0 or `node.deltas[metric]` is less than 0.
    *   Return `false` in all other cases, including when both attributes and deltas are undefined.

*   Update `CodeMapArrowService`:
    *   Add a method `addEdgeMapBasedOnNodes(nodes: Node[]): void` to populate the internal edge map from the provided node array without rendering.
    *   Modify `addEdgePreview()` to accept no arguments and render based on the internal map. Ensure it leaves the map unchanged if called without a prior `addEdgeMapBasedOnNodes`.

*   Adjust the render service method to:
    *   Call `addEdgeMapBasedOnNodes(sortedNodes)` followed by `addEdgePreview()`.

*   Enhance `TreeMapHelper` with a static method `resolveHeightValue(heightValue: number, heightScale: number, node: CodeMapNode, state: CcState): number`.
    *   Return `heightValue * heightScale` if it meets the minimum height.
    *   Use a minimum height floor of 0 for nodes with a delta value and `MIN_BUILDING_HEIGHT` otherwise.

*   Modify `MapTreeViewItemIconColorPipe.transform(node)`:
    *   Return `areMetricZeroColor` for nodes with no attributes, absent area metric keys, or non-negative delta values.
    *   Return the marking color for nodes with a valid negative delta.

*   Update `MapTreeViewItemNameComponent`:
    *   Apply `noAreaMetric` CSS class when the node's delta area metric is zero or positive, or absent.
    *   Do not apply `noAreaMetric` when the delta is negative.

*   Rename the CSS class for search result highlights to `tree-search-result`.

*   Export constants from `dataMocks.ts`:
    *   `VALID_NODE_WITH_PATH_AND_DELTAS` and `VALID_BIG_NODE_WITH_DELTAS` as `CodeMapNode` structures with delta values.

*   When computing delta values for nodes existing in one version:
    *   Set each delta to the negation of the original attribute value and the attribute value to 0.

*   Ensure `deltaGenerator` snapshot test data includes:
    *   A node named 'Cthulhu' with path `/root/onlyA/special/Cthulhu`, with `monster: 1` in one version and `monster: 666` in the other.
    *   A node 'Narwal' with `monster: 42` in one version.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.