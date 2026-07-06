Implement enhancements to the `SiteNode` class to improve context scoping for nodes beneath data-driven placeholders and nodes with parameter annotations in their display names. Ensure that context inclusion and exclusion checks use the correct URL reconstruction for accurate regex matching.

*   Update the `SiteNode` class located at `zap/src/main/java/org/parosproxy/paros/model/SiteNode.java`:
    *   Implement a method `getCleanNodeName()` that returns the bare URL path segment name without HTTP method prefixes or parameter annotations.
        *   Preserve parentheses in the clean name if they are part of the actual URL path segment.
        *   Strip all parameter annotations for nodes with display names like 'METHOD:path(param1)(param2...)'.
    *   Add a four-argument constructor `SiteNode(SiteMap siteMap, int type, String nodeName, String cleanNodeName)` to allow explicit specification of a clean node name.
        *   Ensure `getCleanNodeName()` returns the explicitly supplied clean name when this constructor is used.

*   Ensure context inclusion and exclusion checks handle data-driven placeholder nodes correctly:
    *   Modify context inclusion checks (`isIncluded`, `isInContext`) to reconstruct URLs using the clean node name and represent data-driven segments as wildcards.
    *   Modify context exclusion checks (`isExcluded`, `isInContext` returning false) to similarly reconstruct URLs for accurate exclusion.

*   Ensure context checks for leaf nodes with parameter annotations use the clean node name:
    *   Use the clean node name when reconstructing URLs for regex matching in both inclusion and exclusion checks.

*   Update session-level scope methods:
    *   Ensure `isIncludedInScope`, `isInScope`, `isExcludedFromScope`, and `getContextsForNode` handle nodes beneath data-driven placeholder nodes accurately, reflecting the context-level behavior.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.