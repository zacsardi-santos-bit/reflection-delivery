I'm seeing an issue with how element ordering gets normalized in Excalidraw.

*   The normalizeElementOrder function must place elements from nested sub-groups at their first-occurrence position within the parent group ordering, not at the end of the parent group block.

*   When a sub-group element appears between two sibling elements of the same parent group, normalizeElementOrder must preserve that relative position — the sub-group element must remain between those siblings rather than being relocated.

*   Group identity must be determined by comparing individual group ID strings in the groupIds array, not by concatenating all group IDs into a single string. Groups whose individual IDs happen to share string prefixes (e.g., groupIds ['ab','c','T'] versus ['a','bc','T']) must be treated as distinct groups.

*   Elements with the same group signature at each nesting level must be kept adjacent in the output. For example, two elements both with groupIds ['ab','c','T'] must appear consecutively in the output even if an element with a different inner group (groupIds ['a','bc','T']) was interleaved in the input.

*   In a mixed input where elements from different groups within the same top-level group appear, the output order must follow first-occurrence ordering: the first time a particular inner group is seen, its slot is established; subsequent elements of that inner group fill the same slot, and a new slot is only opened when a new inner group is first encountered.


*   Interface details: Type: Function
Name: normalizeElementOrder
Location: packages/element/src/sortElements.ts
Signature: normalizeElementOrder(elements: readonly ExcalidrawElement[]) => ExcalidrawElement[]
Description: Normalizes the order of canvas elements so that grouped elements appear contiguously and sub-group elements are placed at their first-occurrence position within the parent group, rather than being moved to the end of the parent group. Group identity is based on each individual group ID string (not the concatenation of all group IDs), so groups with different ID arrays are treated as distinct even if their ID strings happen to share prefixes.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.