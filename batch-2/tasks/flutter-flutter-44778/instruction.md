Refactor the fill-viewport sliver widget to directly manage its render object, eliminating unnecessary intermediate wrappers. Ensure the widget and its render object are first-class components, simplifying the render tree and improving debug output.

*   Update the `RenderSliverFillViewport` class:
    *   Accept a named `childManager` parameter of type `RenderSliverBoxChildManager`.
    *   Do not require an `itemExtent` parameter.
    *   Extend `RenderSliverMultiBoxAdaptor` or a compatible subclass.
    *   Remove any `@Deprecated` annotation to ensure it is a fully supported public API.
    *   Ensure the class name appears as "RenderSliverFillViewport" in `toStringDeep()` debug output.

*   Modify the `SliverFillViewport` widget:
    *   Change the base class to `SliverMultiBoxAdaptorWidget` instead of `StatelessWidget`.
    *   Directly create a `RenderSliverFillViewport` render object using `createRenderObject`.
    *   Implement `createRenderObject(BuildContext context)` to return a `RenderSliverFillViewport`.
    *   Implement `updateRenderObject(BuildContext context, RenderSliverFillViewport renderObject)` to update the `viewportFraction` on the render object.

*   Ensure the render tree:
    *   Shows `RenderSliverFillViewport` at the top level when inspected via `toStringDeep()`.
    *   Does not include intermediate `RenderSliverPadding` or `RenderSliverFixedExtentList` nodes.
    *   Displays the 'currently live children' range directly on the `RenderSliverFillViewport` node.
    *   Formats each child's `parentData` as 'index=N; layoutOffset=M.0' using `SliverMultiBoxAdaptorParentData`.

*   Maintain layout geometry:
    *   Each child should receive `BoxConstraints` sized to the full viewport dimensions.
    *   The `scrollExtent` should reflect the number of items times the viewport's main-axis extent.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.