I'm working with our data table component and I've got a batch of sorting features, column drag-and-drop, and some annoying bugs to knock out. On sorting, I want per-column config so clicking a header a third time can reset back to the original unsorted order instead of cycling ascending/descending forever, plus an option that makes the very first click sort descending, and a per-column custom sort comparator so we can pass our own comparison function for a specific column rather than being stuck with just the table-wide one.

I also need drag-and-drop column reordering, which means a new hook module that exposes functions to build a column model from a set of column references, reorder a column order array, and handle hover events while a column's being dragged around.

On filtering, the reset button in the filter dialog should fire its own dedicated callback when clicked without closing the dialog, since right now there's no way to react to a reset separately from a close.

Couple of bugs too. The selection toolbar still renders even when it's set to hidden if rows happen to be pre-selected at init, and it just shouldn't render at all when hidden placement is active. Also when we're in single-row selection mode, trying to programmatically select multiple rows silently accepts it right now, I want it to throw an error instead. And the CSV utility that escapes dangerous characters crashes when it hits a non-string value like a number, so it should just return non-string values as-is without touching them.

These give us finer control over sorting UX and make the table hold up better around edge cases like non-string data, pre-selected rows, and custom filter interactions, and the drag-and-drop bits unlock proper column reordering.
