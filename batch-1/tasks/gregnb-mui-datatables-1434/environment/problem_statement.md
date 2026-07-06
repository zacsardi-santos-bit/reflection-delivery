## Description

The data table library is missing several important sorting and column management capabilities that users have been requesting. Currently, there is no way to configure a column so that its sort order resets to the original (unsorted) state after cycling through ascending and descending. There is also no way to make the first click on a column header sort in descending order rather than ascending. Finally, there is no way to provide a per-column custom sort comparator — only a table-wide comparator exists.

In addition, columns cannot currently be reordered by drag-and-drop, and there is no utility hook to support such functionality.

There are also several smaller gaps: the filter dialog's reset button does not trigger a dedicated callback for callers to respond to; when the select toolbar is configured not to show, it still sometimes renders incorrectly; the selection toolbar does not validate that only a single row is allowed when in single-select mode; and the CSV escape utility crashes on non-string cell values.

## Expected Behavior

- A column option to control whether a third sort click resets to unsorted state
- A column option to make the first click sort descending
- A per-column custom sort comparator function option
- A utility hook for column drag-and-drop reordering, including column model building and hover handling
- A dedicated callback when the filter reset button is pressed (without closing the dialog)
- The select toolbar must not render when its placement is configured to be hidden, even with rows pre-selected
- An error must be thrown if multiple rows are programmatically selected while the table is in single-selection mode
- The CSV character escape function must safely handle non-string cell values like numbers

## Why This Matters

These features give developers fine-grained control over sorting UX and make the table more robust for edge cases like non-string data, pre-selected rows, and custom filter interactions. The column drag-and-drop support enables rich column reordering functionality.
