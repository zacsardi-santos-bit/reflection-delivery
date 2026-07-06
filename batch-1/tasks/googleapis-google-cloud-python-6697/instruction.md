Implement enhancements to the Firestore query cursors to accept a list or tuple of values and ensure proper validation against query order fields. Update the cursor handling to prevent silent errors by enforcing that the order is defined before applying cursors.

*   Modify the `_cursor_helper` method in `firestore/google/cloud/firestore_v1beta1/query.py`:
    *   Accept `document_fields` as a dict, list, tuple, or `DocumentSnapshot`.
    *   Convert tuples to lists and extract dict representation from `DocumentSnapshot`.
    *   Store a deep copy of `document_fields` when it is a list or dict.
    *   Store the cursor pair `(document_fields, before)` in `_start_at` if `start=True`, leaving `_end_at` as `None`.
    *   Store the cursor pair in `_end_at` if `start=False`, leaving `_start_at` as `None`.
    *   Return a new `Query` instance.

*   Update the `_normalize_cursor` method:
    *   Return `None` if `cursor` is `None`.
    *   Raise `ValueError` if `cursor` is non-None but no order fields (`orders`) are defined.
    *   Raise `ValueError` if `document_fields` is a list and its length does not match the number of order fields.
    *   Raise `ValueError` if `document_fields` is a dict missing any required order field key.
    *   Return `(list, before)` unchanged if `document_fields` is a list with the correct length.
    *   Convert a dict cursor to a list of values in order-field order and return `([values_in_order], before)`.

*   Adjust the `_cursor_pb` function:
    *   Accept only `cursor_pair` as an argument.
    *   Return `None` if `cursor_pair` is `None`.
    *   Encode each value in `list_of_values` using `_helpers.encode_value` and return a `Cursor` protobuf with encoded values and the `before` flag.

*   Ensure order-by clause is established before calling cursor methods (`start_at`, `start_after`, `end_before`, `end_at`):
    *   Validate cursor alignment with order fields during query serialization.
    *   Raise `ValueError` if the cursor does not align with the defined ordering.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.