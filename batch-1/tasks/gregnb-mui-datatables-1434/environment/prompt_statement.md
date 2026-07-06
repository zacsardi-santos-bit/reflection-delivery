I'm working with a data table component and I need to add several new features and fix some existing bugs.

For sorting, I want to add the ability to configure individual columns so that clicking a column header a third time resets the sort back to the original unsorted order (rather than cycling between ascending and descending forever). I also want an option to make a column sort in descending order on the first click. Additionally, I need support for a per-column custom sort comparator function, so developers can supply their own sorting logic for specific columns.

I also need to add support for reordering columns via drag and drop. This requires a new hook module that exposes functions to build a column model from column references, reorder a column order array, and handle hover events during dragging.

On the filter side, the reset button in the filter dialog should trigger a dedicated callback when clicked, without closing the dialog. Currently there's no way to respond to a filter reset separately from closing.

There's also a bug where the selection toolbar still renders even when it's configured to be hidden, if rows are pre-selected on initialization. It should not render at all when hidden mode is active.

When the table is in single-row selection mode, attempting to programmatically select multiple rows should throw an error rather than silently accepting the input.

Finally, the CSV utility function that escapes dangerous characters currently crashes when it encounters a non-string value like a number. It should return non-string values as-is without modification.
