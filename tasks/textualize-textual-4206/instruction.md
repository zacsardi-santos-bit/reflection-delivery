Implement a "recompose" mode for the Textual TUI app's reactive system to allow automatic rebuilding of a widget's child hierarchy when a reactive attribute changes. This will eliminate the need for separate watcher methods and integrate seamlessly with the existing data binding mechanism.

*   Update the `reactive` descriptor in `src/textual/reactive.py`:
    *   Accept a `recompose` keyword argument, defaulting to `False`.
    *   When `recompose=True`, ensure any change to the reactive attribute's value triggers a full recomposition of the owning widget by calling its `compose()` method.

*   Modify the `Widget` class in `src/textual/widget.py`:
    *   Update the `refresh` method to accept a `recompose` keyword argument.
        *   When `recompose=True`, schedule a recomposition by removing current children and remounting new ones via `compose()`.
    *   Implement an async `recompose` method:
        *   Remove all non-system child widgets (those not having the `.-textual-system` CSS class).
        *   Call `compose()` to remount the new children within a single batched update.
        *   Ensure recomposition occurs after any currently queued updates to batch multiple rapid changes.

*   Ensure compatibility with `data_bind`:
    *   When a parent reactive with `recompose=True` is bound to a child widget, the child must recompose and reflect the new value correctly upon changes.

*   Verify that widgets using reactive attributes with `recompose=True` render correctly after recomposition, matching the visual output of a freshly mounted widget with the same values.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.